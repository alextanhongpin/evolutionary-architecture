# Infrastructure in Evolutionary Architecture

From the Clean Architecture, we know that most services require at most the following:

```js
const { Car } = require('entity')
const db = new Datebase()
const repository = new Repository(db)
const useCase = new UseCase(repository) // or Service.
useCase.getCar() // Returns the car entity.
```

But how about other infrastructure like `logging`, `caching`? 

## Logging

We can introduce the logging as part of the `UseCase/Service`. 


```js
class CarService {
   constructor(repository) {
    this.repository = repository
   }
   // eventCarDeleted
   // eventCar...
   eventCarCreated(name: string, id: number) {
      console.log({
        event: 'car_created',
        name,
        id
      })
   }
   async createCar(name: string) {
      const car = new Car(name)
      // HINT: The model is responsible for validating the domain logic. 
      // But services can provide additional logic that cannot be performed at the model level.
      if (!car.valid()) {
        throw new Error('invalid car')
      }
      const id = await this.repository.insert(car)
      
      // Log the event.
      this.eventCarCreated(name, id)
      return car
   }
}
```

Or we could group them as another standalone `entity`:

```js
// We can now easily replace logger with a NOP (null object pattern) logger.
class LoggerNop {
   carCreated() {}
}

class Logger extends LoggerNop {
   constructor(log) {
      this.log = log
   }
   carCreated(id: number, name: string) {
      this.log.info({id, name}, 'car created')
   }
}

class CarService {
   constructor(repository, logger) {
      this.repository = repository
      this.logger = logger
   }
   createCar() {
      // ...
      this.logger.CarCreated(id, name)
   }
}

const carService = new CarService(new Repository(), new Logger())
const carService = new CarService(new Repository(), new LoggerNop())
```

## Caching

For caching, we can use `decorator` or `proxy` pattern.

```js
const Cache {
   constructor(cache) {
      this.cache = cache
   }
   getCar(id: number) {
      const car = this.cache.get(id)
      if (car) {
         return car
      } 
      return null
   }
   setCar(id: number, car: Car) {
      this.cache.set(id, car)
   }
}


class CarService {
   constructor(repository, cache) {
      this.repository = repository
      this.cache = cache
   }
   // Actual DB call.
   async _getDbCar(id: number) {
      const car = await this.repo.getCar(id)
      this.cache.setCar(id, car)
      return car
   }
   async _getCacheCar(id: number) {
      return this.cache.getCar(id)
   } 
   async getCar(id: number) {
      const car = await this._getCacheCar(id)
      if (car) {
         return car
      }
      // Calls the DB.
      return this._getDbCar(id)
   }
}
```

## Scoping Dependencies

It is hard to scope dependencies. Often we are left with two choices (there are probably more, but I'm restraining the scope here). One is to create a `class` or `struct` (in _Go_) that will store a reference to the dependencies in the constructor, or another is just to _curry_ the dependency using higher order functions. Both have their pros and cons.

Namespacing with `class` pros:
- Can define an interface for all it's operation
- Methods simple depend on the dependency without needing to know them
- All methods share the same dependency
- Less code, looks flat :D

Namespacing with `class` cons:
- Coupling to a namespace when it's unnecessary
- Dumb class - the class is doing nothing
- Even when only one method is required, we have to initialize the whole dependency (this is obvious in testing)

Higher-order currying pros:
- Only initialize the function you need
- Can be initialized with the exact dependencies, and the function requires all the dependency unlike `class`, where not all dependencies are required by the methods.
- Easier to mock individual functions. Say I want to mock one function that depends only on a database, but in the `class`, I need to initialize the `logger`, `message queue` etc for the whole class. 

Higher-order currying cons:
- Must be initialized many times, and someone has to hold the reference
- more repetitive code with the same initialization methods

### Example: Repository Class
The key here is to know which to pick. Say, for a `repository`, defining it as a `class` is more appropriate, since all methods has to depend on the `database`:
```js
class Repository {
   constructor(sql) {
      this.sql = sql
   }
   getBooks() {
      // Do something with sql.
   }
   getBook(id) {}
   updateBook() {}
}
```
As opposed to this which has more repetitive class:
```js
function getBooks(sql) {
   return function () {
      // Do something with sql.
   }
}
function getBook(sql) {
   return function () {}
}
```

The cache layer can be a separate class, and can be implemented using decorator or proxy pattern instead. 

### Example: UseCase/Service

Use cases can depend on many dependencies, such as `logger`, `repo1`, `repo2`
```js
function handlePaymentBuilder({logger, queue, userRepo, paymentRepo}) {
   return async function (req) {
      // Validate.
      const user = await userRepo.getUser(req.id)
      if (!user.valid()) {
         throw new Error('invalid user')
      }
      // Create Payment.
      const success = await paymentRepo.create({id: user.id, product_id: req.id})
      
      // Log the payment request, and response.
      const ctx = {user, req}
      logger.paymentCreated(ctx)
      
      // Send the event to the queue.
      await queue.send(ctx)
      
      return success
   }
}
// Usage.
const handlePaymentService = handlePaymentBuilder({logger, queue, userRepo, paymentRepo})
await handlePaymentService({
   id: 1,
   product_id: 10
})
```

If we have 10 different service, we need to initialize them 10 times. To simplify things, we can attach the created service to an object:

```js
interface PaymentServiceOptions {
   UserRepo: UserRepo;
   PaymentRepo: PaymentRepo;
   Logger: PaymentLogger;
   Queue: Queue;
}

interface PaymentService {
   HandlePayment(req: HandlePaymentRequest): HandlePaymentResponse
   // Add more methods here.
}

const Payment = {
   new: (opts: PaymentServiceOptions): PaymentService {
      return {
         handlePayment: handlePaymentBuilder(opts)
      }
   }
}
```

## References
- https://stackoverflow.com/questions/27654014/where-logging-should-go-in-onion-architecture-with-ddd
- http://hannesdorfmann.com/android/evolution-of-the-repository-pattern
- https://medium.com/@krzychukosobudzki/repository-design-pattern-bc490b256006
- https://stackoverflow.com/questions/13633201/service-layer-and-repositories-relationship
- https://softwareengineering.stackexchange.com/questions/345661/is-a-facade-repository-an-ok-design


Infrastructure management

What is the proper way to manage infrastructure at scale?
- how to find out cost of infra
- how to design infrastructure when working in teams
- how to debug errors in infrastructure
- how to document them
- Business use case
    - what value does this feature add?
- Documenting apis (using swagger)

Naming things
- Environment naming
- Application naming
- Tags

- design an endgoal
- create roadmaps and milestones
