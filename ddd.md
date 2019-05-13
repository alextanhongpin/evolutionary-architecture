

## Entity

Entity has 
- an identity (id, can be unique, can be primary, foreign keys or combination of keys)
- has behaviour (that modifies itself)
- has attributes (that defines itself)

## Value Objects

- compared with values only
- does not have an identity (e.g. Money(5) is the same as Money(5) even if they are two different objects)
- usually used to define types that are 1 level above primitives (int, string etc). E.g. use Age instead of primitive integer, because Age cannot be negative, have a boundary value, etc
- Note that some enums are just comparable in terms of equality, they may also be orderable (lesser, greater, equal). 
- are DTO value objects? Data transfer object (DTO) are normally our request/response pair, where they are just responsible for transferring data/performs serialisation/deserialisation without carrying any other behaviour/business logic. 

## Service

- is stateless
- can perform operation on an entity 
- use to orchestrate interaction between different entities (e.g. order item), like a saga orchestrator (?)
- if the behaviour does not belong to an entity, it will normally be implemented here
- a.k.a usecases
- may sound like aggregate, but it is actually different. Aggregate root manages a bunch of entity and ensure the state is valid/consistent.

## Aggregrates

- can be a combination of one or more entities
- is defined as a cluster of associated objects that we treat as a unit for the purpose of data changes
- performs actions between entities that are related to one another

https://medium.com/@unmeshvjoshi/aggregate-oriented-microservices-d314eb04f2b1
https://softwareengineering.stackexchange.com/questions/237943/domain-services-vs-factories-vs-aggregate-roots

```go
package main

import "fmt"

type Person struct {
	Name string
	Age  int
}

type PeopleManager struct {
	people []Person
}

func (p *PeopleManager) Adults() []Person {
	var result []Person
	for _, i := range p.people {
		if i.Age > 10 {
			result = append(result, i)
		}
	}
	return result
}

func main() {
	people := []Person{
		{"john", 10},
		{"jane", 20},
	}
	pm := PeopleManager{people}
	adults := pm.Adults()
	fmt.Println(adults)
}
```


### Things to consider when designing a model

- the attributes
- the identity
- the behaviour
- the association
	- single entity. can it be one, can it possibly be two, can it be a fixed number, can it be unlimited?
	- multiple entity (e.g. a car and engine can have only one car_engine). can it be one, can it be ...
- uniqueness
- aggregates (what entity is dependent on another)
- saga + state machine (what interaction/steps/process flow that is required in order to reach the final state)
- is it an immutable object
- does it have a status? do we need to keep track of the status
- does it have a sequence of state that can change over time? can it be a time series?
- does it have a validity range (valid from, valid till)? can it expire?
- when it expires, does it still play a role (historical/readonly, but no actions). E.g. pricing information
- does it need versioning? version 2 wil have a different business rules from version 1. version 1 is deprecated, version 3 is created, and version 3 can coexists with version 2.
- which is the aggregate root? when deleting the root entity, how does it affect the rest? will they be deleted?
- are the entity immediately related? or can they be delayed? (e.g. order can be created, but the payment can be later. once the payment is completed, it needs to be verified, but it will also depends on the admin that manually updates it).
- let's break it down, it can have diferrent statuses, relationships, lifecycle, version

https://airbrake.io/blog/software-design/domain-driven-design

## Other userful terms

We can probably break down our services into the following:

- Service orchestration is the coordination or integration of several services to expose it as a single service.
- Service aggregation act as a facade over the lower level services, hiding technical details and interrelationship from the service consumer. Service aggregation adds alot of value to the overall system by providing a higher level of abstraction. Tool: Oracle Service Bus (OSB-Proxy Services), for example.
- Service Augmentantion is used within a service for message enrichment. Suppose that based on client id you need to obtain additional data, like address, email or phone, you should augment message using database queries, etc..

It seems like most of the services (CRUD) written can be classified as service augmentantion. For most CRUD, the service layer tends to be redundant, since it is only a layer that makes a call to the repository. This is especially more apparent for services that are mainly GET (getters).


## Similarity between Clean Architecture (CA) and Onion Architecture (OA)

Some say that CA is the concept, OA is the implementation. 

## Code Structure

NOTE: Opinions might differ. This are merely assumptions I made when working on different architecture. The original author's opinion might differ greatly.

From the bird's eye view, there are a few meaningful layers for the architecture:

- infrastructure
- presentation
- domain

The `infrastructure` layer is responsible for the initialisation and creation of all the dependencies required for the application. This includes config, persistence (see a longer list below) etc. The dependencies can be initialised lazily for singletons so that it is only created when needed, and created only once. This is particulary useful when the same dependency is shared by multiple other layers that requires the parent dependency to be initialised first. By loading the dependency lazily, we can avoid coupling the build sequence. 

Take the `repository` and `database` deps (dependencies) for example. We have `UserRepository` and `BookRepository`, which requires the `mysql` dep. In order to create both repositories, we have to initialise the `mysql` first. But if we lazily load the deps when creating the `UserRepository`, we do not need to care about the sequence:

```js
// Here the resource creation is coupled, and the sequence matters too. With this, we have to initialise 
// all the dependencies in a single location.
const db = NewDB()
const userRepo = new UserRepository(db)
const bookRepo = new BookRepository(db)

// vs
class Factory {
	// Singleton. NOTE: This is not thread-safe. This may be called multiple times, hence creating the db
	// connection twice and replacing the old db connection. We need a mutex to ensure the db is created 
	// only once.
	async db() {
		// Once only initialisation.
		if (this.db) {
			return this.db
		}
		// Wrap this in async mutex.
		this.db = await NewDB()
		return this.db
	}
	async newUserRepository() {
		return new UserRepository(await this.db())
	}
	async newBookRepository() {
		return new BookRepository(await this.db())
	}
	async newUserService() {
		return new UserService(this.newUserRepository())
	}
}
const factory = new Factory()
// Resources can be created without knowing the relationship between different dependencies.
// Dependencies can be created anywhere at ease without worrying about missing dependencies.
factory.newUserService()
factory.newBookRepository()
```

The example above shows the creation of the `UserService`, but behind the scene, the different layers are created differently. How is this different than creating the `UserService` in a single function?

```js
const newUserService = (db) => {
	const repo = new UserRepository(db)
	const service = new UserService(repo)
	return service
}

const bookService = (db) => {
	// Duplication.
	const userRepo = new UserRepository(db)
	const bookRepo = new BookRepository(db)
	const service = new BookingService(userRepo, bookRepo)
	return service
}
```
The previous approach has the advantage of being able to recreate intermediate layers (the `UserRepository`) when needed. This reduces code duplication, especially when the `UserRepository` needs to be used by a service layer other than `UserService`. This makes composition much easier and less coupled to the creation.

### Infrastructure and Globals
The `infrastructure` also avoids the global variables. This layer also have knowledge on the type of dependency created:

- singleton enablers: e.g. Resource that needs to be created only once, and reused. E.g. Database connection, loading configuration from environment variables etc. Note that creating multiple infrastructure means having multiple singletons too.
- dynamic resource: Resource that can be created multiple times, and each are different from the rest.  

The dependencies are created though a `Container` (just a naming), which holds different factories. Here we are seeing several design patterns in use:

- singleton
- lazy initialisation
- factory
- builder
- mutex/sync.Once (related to singleton concurrency pattern)
- others ...

### Infrastructure Lifecycle

The most important thing about the creation of infrastructure is the destruction of it. Resources that are created needs to be destroyed/gracefully shutdown at the end of the application lifecycle. Hence, the infrastructure is responsible to coordinate the shutdown/termination of all the infrastructures that are created. This could be important if the infrastructure needs to be terminated sequentially, or if there's a timeout period for (one/many) infrastructure (useful for Kubernetes graceful shutdown).

```js
class Infrastructure {
	// Array that stores the shutdown methods
	supervisor = []
	db () {
		if (this.db) return this.db
		this.db = new DB(this.config.db)
		// Adapter pattern to ensure that the shutdown function has the same method.
		this.supervisor.push(() => this.db.close())
		return this.db
	}
	onShutdown(shutdown) {
		this.supervisor.push(shutdown)
	}
	// This is called at the end to coordinate shutdown of singletons. Dynamic resources that has shutdown methods
	// needs to be added separately.
	async shutdown () {
		try {
			await Promise.all(this.supervisor.map(fn => fn()))
			// exit
		} catch (err) {
			// exit
		} finally {
			// cleanup.
		}
		//for (const shutdown of this.supervisor) {
		//	await shutdown()
		//}
	}
}
```

### Breakdown

`infrastructure` can be divided into two different parts, `factories` or `singleton` enablers. For most application, there are enablers, dependencies that are required before the application can function (persistence layer, router).  Enablers are mostly singletons, they are only initialised once and are reused for most application. E.g. database pool connection, distributed cache connection, message queue, logger etc. Factories on the other hand can create new instances of the object/classes to be used. 

Infrastructure Layer
- Supervisor (Activity Manager): coordinates the shutdown of all the infrastructure. External shutdown func can be added to (server shutdown). For singleton infrastructure with a shutdown method, they can be added when they are initialised once. 
- Logger: logger can be created and initialised here, but we can also set a global one. But in this case, better to pass them down through dependency injection.
- Persistence (database, file storage, in memory, key-value database): The persistence layer for different db (or just one). This just initialise the db connection. What if we need multiple db connection? Create two factory. 
- Configuration: The configuration can be global (all put in one config), package level (each package has their own config). global and local config.
- Secrets: Are usually loaded separately from external vaults. Avoid plain text secrets in configuration that is loaded through environment variables.
- Message Queue: initialising the message queue, shutting down the queue.
- Background Threads: Manages background channel. Requires monitoring/heartbeat, and proper shutdown to avoid leaking goroutines.
- Cron: Runs at periodic time. 
- Container: The infrastructure core is called the container.
- Mailer: the service to send email, may have a use case that is calling this to send email.
- External Service Adapters (a.k.a. Clients): External http calls should be wrapped in a struct to enable them to be monitored for errors, retry (for idempotent requests only), timeout etc.
- Factories: The factories (or container dependency injection) for creating all the infrastructure, services, controllers, router and server). It should be responsible for bringing the infrastructure down too (coordinating shutdown). If they have a shutdown method, they need to be added manually in order to invoke the shutdown, since we do not know how many instances can be created
	- Repository Factory: infrastructure is responsible for creating all the services and repositories. They should be created independently, since a service may use several repositories. 
	- Service Factory
	- Use Case Factory
	- Singleton Factory
	
- ~~Router (server)~~. Router are normally part of a framework, and we want to avoid coupling the framework to our infrastructure, since that may change. The same goes for controllers which has the interface defined by the framework. Put only reusable components in the infrastructure declaration. 
- ~~Controller~~ (NOTE: Previous design couples the controller with the services and repositories, probably I should remove the coupling now. But putting it together in the same package makes it reusable if we are going to reuse the same framework if we have already decided on that). Reason to put controller together in the `domain` package - they act as a feature and is fine as long as we only have one transport http layer. The reason to separate them is when we have different transport layer such as grpc, websocket etc.

Presentation Layer (UI)

APIs are considered part of the UI layer, even when the name can be a little misleading.

- Api: Api layers contains the standard api error domain model, status code, DTO definition, could possibly handle serialization/deserialization of requests, and provides generic requests/response methods.
- Middlewares: Middleware logic such as logging, retry, rate limiting, circuit breaker, request id, authorization, role and scope checking, secure http headers, cors. Middleware layers should be dumb, does not contain business logic and is just a layer that acts as guard/funnel to filter/parse requests. Security layers can be added here too (AppSensor). Note that many of this capability can be handled externally through sidecars/gateway etc. Just use what is sensible.
- Controllers:  Endpoints for different service APIs. Controllers definition are normally framework dependent, hence a separate layer from the domain layer to avoid coupling. Controllers are dumb too - they should not perform any business logic. They can however map requests body/headers, perform serialization/deserialization, returns response body etc. It is best to avoid any conditionals in the controller layers too (e.g. when checking roles etc). 

Domain Layer
- Entity/Model: This is where the business logic is encapsulated. Avoid anemic data model. Value objects are commonly used as enums, modelling currency etc. This layer should be business logic rich.
- Service: Business logic that is external to the domain model is normally implemented here, such as calling the persistence layer to check if the user already exists. Since the entity does not have knowledge of the persistence layer, the service will hold the logic. Each service is tied to the aggregate root (!not entity, but in most cases it can tied to a single entity too). Services do not call each other. Each service performs only a single action. Different services can be called by a usecase to coordinate more complex business logic. Service layer can normally be omitted, if they are mainly making a single call to the repository. The reason why this layer exists is mainly for code-reuse. And sometimes we want to avoid coupling the business logic to the repository layer, though this is common when dealing with database, e.g. checking count, filtering with conditions is all done at the persistence layer, not service actually. See the example below on `Whe Service layer`...
- Repository: Repository is similar like an ORM layer. It hides the complexity of reading data from the data store. This is normally a separate layer to make testing easier too - the layer can be mocked with static data or in-memory persistence layer. The point of repository is not to make it easier to swap the persistence storage, swapping a nosql and mysql normally involves a rewrite of the repository layer due to the difference in interface.
- Use case: Probably the most confusing layer, as it is always confused with the service layer. They seem to have overlapping responsibilities, and sometimes the service layer can be omitted too (if the responsibility is only for a single function and there is not potential reuse). Coordinates multiple layers to fulfil a business logic. Can call repository, service and other use cases (in UML there is the include/extend use case)! May contain additional business rule validation. Note that this usecase is only scoped to the current domain (local usecase). If there are use cases that requires chaining/coordination/orchestration with other domain's use case (global use case), they can be defined in another layer (application folder). Relative to service, use case is always one layer above service.

Application Layer
	- use case: The use cases for the domain. A service does one thing only (checking if email exists for user in the persistence layer, if the role is correct), the use case coordinates multiple services and repositories. Why do we define the use case here, and not in the package level? Because we may have external use cases here that needs to be shared, e.g. mailing. Delivering email should have it’s own domain for reusability, and it should be callable from the other use cases. Booking + send email + log events.
        - The use case in this folder can call all the other services/usecases that are available. Note that this might create some coupling between services, but the boundaries should be clearly defined too.

Some thoughts:
- to keep code modular, we can write modules (or packages, `pkg/` in golang). Just remember that there are package level behaviour, and application level behaviour. Even when the package delivers the value immediately, we might want to add another additional layer to abstract the operation of the package. This makes the packages easier to substitute in the future, and the whole application structure more framework-friendly (Plug and play architecture, if you may). Only add those layers if it is necessary. Often the additional layers only adds complexity.


### Why Service Layer

Separate the business logic from the repository layer and place it in the service instead to make it more reusable. E.g. checking the count to determine if the user has internet quota:
```js
// Bad. Coupling business logic with database. What if I need to know the quota available, or if different plans have different quota requirements?
const hasSufficientQuota = await repo.hasMoreThan100MB(userId)

// Better. Get the remaining quota, perform business logic.
const quota = await repo.getQuota(userId)
if (quota > 100) { // low usage 
}
if (quota > 200) { // high usage
}
const remainingQuota = MAX_QUOTA - quota
```

## Do we need all the layers?

If we only have plain CRUDs, the Service and UseCase level becomes redundant, since they will be a dumb layer that are just responsible for delegating the requests/responses. In that scenario, we can skip the layers alltogether. In a project, once we identify which layers can be skipped (CRUD layer without business logic), it is much more sensible to group them together.

## Relationship between services

Mapping relationship between services. Yeah, services should be independent, but there are times where we need to call different services to get the job done. E.g. creating order (order service) and sending email (email service). Or approving employee payroll (employee service), checking the role of the employee (employee role service) and sending out email notification (mail service). 
The initial thoughts is to have a mediator, or facade pattern to hide the complexity. But in order to do so, both the services should be grouped under one parent, whose hiearachy is higher than both of that.


## Graph relationship

- Controllers may have middlewares, can call service or usecases
- Use case can call different services, repository
- Services can call repository only
- Repository can serialize the db objects back to entity, or deserialize to store it

## References
- https://pusher.com/tutorials/clean-architecture-introduction
- https://crosp.net/blog/software-architecture/clean-architecture-part-2-the-clean-architecture/
- own experience :smile:
