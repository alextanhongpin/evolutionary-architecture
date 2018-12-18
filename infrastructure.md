# Infrastructure in Evolutionary Architecture

From the Clean Architecture, we know that most services require at most the following:

```
const { Car } = require('entity')
const db = new Datebase()
const repository = new Repository(db)
const useCase = new UseCase(repository) // or Service.
useCase.getCar() // Returns the car entity.
```

But how about other infrastructure like `logging`? We can introduce the logging as part of the `UseCase/Service`. 

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

## References
- https://stackoverflow.com/questions/27654014/where-logging-should-go-in-onion-architecture-with-ddd
