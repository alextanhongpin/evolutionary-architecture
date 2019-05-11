# Infrastructure

This example will answer the following questions:
- how to separate infrastructure implementation details from the application (explanation: most infrastructure setups are mostly boilerplate codes - setting up database, loading config/secrets, setting up jwt authorization etc)
- how to structure the code to make it reusable
- how to avoid magic globals, and circular dependency in the code structure
- how to use dependency injection correctly
- how to use lazy initialisation for infrastructure
- how to initialise struct instances with a lot of dependencies
- how to shutdown gracefully

## Types of instantiation

- singleton: The resources are created once only, and are reused between calls. E.g. database connection, loading config from environment variables
- factory: Returns a new instance for every calls. 

We can differentiate both of them through a more standardise naming convention:

- singleton: `.Database()`, `.Config()`, `.Logger()`
- factory: `.MakeUserService()`, `.MakeUserRepository()`

## Options

There are many ways to define the infrastructures components:

- orchestrate them in the `main` function
- use a DI(dependency injection) framework, such as [wire](https://github.com/google/wire)

## What are infrastructure logic?

Here's how the folder structure may look like for a `go` project. Each file that belongs to this folder can be categorised as part of the infrastructure. The infrastructures are normally the outermost layer (can be before the `ui` layer) of any application (as you can see in the clean architecture).

```
- infrastructure/
  - config.go
  - infrastructure.go
  - database.go
  - queue.go
  - cache.go
  - logger.go
  - client.go
  - supervisor.go
  - cron.go
  - background_worker.go
```

## Code

We usually define golang `factory` through the function with the method `New<Model>`, e.g. `NewDB`, `NewUserService` etc. But as the dependencies grow, we need to pass down more dependencies to instantiate a new struct.

To avoid issues such as circular dependency, magic globals (whether through global variables or `init`) etc, we use `dependency injection` to pass down the dependencies through different layers. For most projects, I find that it works well just defining the dependencies in the `main` function, even though it might result in a more cluttered `main` function. Also, dependencies are coupled to each other more strongly in a sense that we need to know the graph relationship between dependencies when initialising them. One of the hardest part is to coordinate the graceful coordination of the dependencies. The pattern below is meant to address the shortcomings mentioned above.
 
Patterns used: `abstract factory`, `builder`, `dependency injection`, `lazy initialization`, `singleton`
```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

type FakeDB struct{}

func NewDB() *FakeDB {
	return new(FakeDB)
}

func (db *FakeDB) Close() error {
	time.Sleep(1 * time.Second)
	fmt.Println("shutting down db")
	return nil
}

// Shutdown represents the shutdown function to coordinate the shutdown.
type Shutdown func(context.Context)

// Supervisor holds the shutdown methods and executes them before the program terminates.
type Supervisor []Shutdown

// Add appends a new shutdown function to the current list of shutdown methods.
func (s *Supervisor) Add(shutdown Shutdown) {
	*s = append(*s, shutdown)
}

// Shutdown loops through each shutdown methods and gracefully shuts them down within the given context.
func (s *Supervisor) Shutdown(ctx context.Context) {
	for _, shutdown := range *s {
		shutdown(ctx)
	}
}

type (
	UserRepository interface {
		Create()
		Read()
		Update()
		Delete()
	}
	UserRepositoryImpl struct {
		db *FakeDB
	}
)

// NewUserRepository creates a new repository with the given database connection.
func NewUserRepository(db *FakeDB) *UserRepositoryImpl {
	return &UserRepositoryImpl{db}
}

func (u *UserRepositoryImpl) Create() {}
func (u *UserRepositoryImpl) Read()   {}
func (u *UserRepositoryImpl) Update() {}
func (u *UserRepositoryImpl) Delete() {}

// UserService depends on UserRepository
type UserService struct {
	repository UserRepository
}

// Container holds all the infrastructure code and allows the creation of new dependencies or retrieval of existing ones.
type Container struct {
	// Supervises all the shutdown by looping through all the shutdown methods and executing them.
	onceShutdown sync.Once
	supervisor   Supervisor
	// Required for services that needs to be instantiated once (singleton enablers).
	onceDB sync.Once
	db     *FakeDB
	// If it's a factory (not enabler), we don't need to store a reference to it.
	// userRepository UserRepository
	// userService    *UserService
}

func NewContainer() *Container {
	return &Container{}
}

// Shutdown triggers the shutdown of all the initialised infrastructure.
func (c *Container) Shutdown(ctx context.Context) {
	// Ensure that the services are shutdown only once.
	c.onceShutdown.Do(func() {
		c.supervisor.Shutdown(ctx)
	})
}

// DB is created once (singleton enabler).
func (c *Container) DB() *FakeDB {
	// We only need a single instance of the database for each application, hence a singleton here.
	// If the db is not yet initialised, initialise it first, and add the shutdown method to the
	// supervisor. Note that the infrastructure may have different shutdown function definition,
	// and therefor the adapter pattern is used to match the interface.
	c.onceDB.Do(func() {
		db := NewDB()
		// Adapter pattern, graceful shutdown.
		c.supervisor.Add(func(context.Context) {
			db.Close()
		})
		c.db = db
	})
	return c.db
}

// UserRepository can have multiple instances (factory).
func (c *Container) MakeUserRepository() UserRepository {
	return NewUserRepository(c.DB())
}

// UserService can have multiple instances (factory).
func (c *Container) MakeUserService() *UserService {
	return &UserService{
		repository: c.UserRepository(),
	}
}

func main() {
	// Create the infrastructure container that initialises the dependencies lazily.
	c := NewContainer()
	defer func() {
		ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
		defer cancel()
		c.Shutdown(ctx)
		fmt.Println("shutting down")
	}()

	userService := c.MakeUserService()
	_ = userService
	// do something with user service.
}
```

## Other scenarios

Most of the time, this is what we need to do if the infra contains many layers - creating the resources layer by layer. But this results in a lot of boilerplate code. And what if another repository needs to use the same db connection? It means the `db` instance needs to be created outside.
```go
cfg := NewConfig()
db := NewDB(cfg)
repo := NewRepository(db)
svc := NewService(repo)
ctrl := NewController(svc)
```

We can simplify it this way:
```go
func NewController(db *sql.DB) *Controller {
    repo := NewRepository(db)
    svc := NewService(repo)
    ctrl := NewController(svc)
    return ctrl
}
```

But what if another service needs to use the same service that is created? We will end up redeclaring them many times. With the `Container` implementation though, we can simplify it:

```go
func (c *Container) RepositoryA() *RepositoryA {
    return NewRepositoryA(c.DB())
}
func (c *Container) ServiceA() *ServiceA {
    return NewServiceA(c.RepositoryA())
}
func (c *Container) ServiceB() *ServiceB {
    // I can just call service B without knowing what are the dependencies that needs to be initialised.
    return NewServiceB(c.RepositoryA())
}

// Instantiation of services without needing to know what dependencies are required to build them.
_ = c.ServiceA()
_ = c.ServiceB()
```

## Graceful shutdown

We standardise our shutdown method for each dependencies by declaring the type of the function. The dependencies with different shutdown mechanisms can then conform to the interface (adapter pattern).

There are different approaches to coordinate the shutdown:

- shutdown all at the same time, regardless of sequence within a given time limit. This works if the dependencies are not related to one another.
- shutdown each dependencies sequentially, each with their own time limit. Note that if each of them uses the max time, the shutdown period will be very long.

Graceful shutdown of the server is common. But most of the time, there are other infrastructure that requires graceful shutdown too:

- logs: buffer needs to be cleared before shutting down to avoid loss of logs.
- database: connection(s) needs to be terminated when the application stops.
- background worker: processing running in a separate threads (or goroutines) needs to be terminated gracefully.
- channels: channels needs to be closed so that new messages should be dropped, and messages that are in the queues are processed completely.
- message queue, distributed cache…etc.
