## Sample SagaOrchestrator

The Orchestrator does not have any business logic, it's just a dumb orchestrator that listens to Events and executes Command that either brings the state forward to completion or reverse them back/compensate the actions that has been taken.
```js
// We have a saga booking system, whereby we perform the following in sequential order
// - (start booking -> {booking_started}) This is the initial starting condition
// - book hotel -> {hotel booked, cancel hotel booking -> hotel booking cancelled}
// - book car -> {car booked, cancel car booking -> car booking cancelled}
// - book flight -> {flight booked, cancel flight booking -> flight booking cancelled}
class SagaOrchestrator {
  constructor(queue) {
    this.queue = queue

    // Book hotel -> book car -> book flight.
    // this.queue.on('booking_started', this.bookHotel)
    this.queue.on('hotel_booking_cancelled', this.terminateBooking)
    this.queue.on('hotel_booked', this.bookCar)
    this.queue.on('car_booking_cancelled', this.cancelHotelBooking)
    this.queue.on('car_booked', this.bookFlight)
    this.queue.on('flight_booking_cancelled', this.cancelCarAndHotelBooking)
    this.queue.on('flight_booked', this.completeBooking)
  }
  start(msg) {
    this.bookHotel(msg)
  }
  bookHotel(msg) {
    this.queue.publish('book_hotel', new BookHotelCommand(msg))
  }
  bookCar(msg) {
    this.queue.publish('book_car', new BookCarCommand(msg))
  }
  bookFlight(msg) {
    this.queue.publish('book_flight', new BookFlightCommand(msg))
  }
  cancelHotelBooking(msg) {
    this.queue.publish('cancel_hotel_booking', new CancelHotelBooking(msg))
  }
  // ... rest not shown
}
```

How the HotelService would look like. Note that there are two important function here:

- the booking of the hotel 
- the cancel booking (compensation function)

An action from a Command will normally produce an Event, whether it is a successful one or not. This Event can be used to signal completion, or to trigger further Commands, hence chaining them until the process is completed.

```js
class HotelService {
  constructor(queue, repository) {

    this.repository = repository
    this.queue = queue

    // Listen to commands, publish events.
    this.queue.on('book_hotel', this.bookHotel)
    this.queue.on('cancel_hotel_booking', this.cancelHotelBooking)
  }
  bookHotel(data) {
    try {
      const result = await this.repository.create(data)
      this.queue.publish('hotel_booked', {
        ...data,
        ...result
      })
    } catch (error) {
      this.queue.publish('hotel_booking_cancelled', data)
    }
  }
  cancelHotelBooking(data) {
    try {
      const result = await this.repository.cancel(data)
      this.queue.publish('hotel_booking_cancelled', result)
    } catch (error) {
      // retry, this should not failed.
      // this.queue.publish('')
    }
  }
}
```
How the FlightService could look like:
```js
class FlightService {
  constructor(queue, repository) {
    this.repository = repository
    this.queue = queue
    this.queue.on('book_flight', this.bookFlight)
    this.queue.on('cancel_flight_booking', this.cancelFlightBooking)
  }
  bookFlight(data) {
    try {
      await this.repository(data)
      this.queue.publish('flight_booked')
    } catch (error) {
      // Skip the command, straight produce the events. When we produce commands, we expect an action to be carried out, when we produce events, it means an action has been completed.
      this.queue.publish('flight_booking_cancelled')
    }
  }
  cancelFlightBooking(data) {
    await this.repository.cancelFlight(data.flightId)
    this.queue.publish('flight_cancelled')
  }
}
```
