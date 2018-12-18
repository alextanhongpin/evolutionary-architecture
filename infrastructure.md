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

## References
- https://stackoverflow.com/questions/27654014/where-logging-should-go-in-onion-architecture-with-ddd
