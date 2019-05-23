## Sample SagaOrchestrator

```js
class SagaOrchestrator {
  setQueue(queue) {
    this.queue = queue
    // Book hotel -> book car -> book flight.
    this.queue.on('booking_started', this.bookHotel)
    this.queue.on('hotel_booked', this.bookCar)
    this.queue.on('car_booked', this.bookFlight)

    // Cancel booking.
    this.queue.on('cancel_hotel', this.cancelHotel)

    // Cancel hotel.
    this.queue.on('cancel_car', this.cancelCar)

    // Cancel car, cancel hotel.
    this.queue.on('cancel_flight', this.cancelFlight)
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
}

// FlightService.
class FlightService {
  constructor(queue, repository) {
    this.repository = repository
    this.queue = queue
    this.queue.on('book_flight', this.bookFlight)
    this.queue.on('flight_cancelled', this.cancelFlight)
  }
  bookFlight(msg) {
    try {
      await this.
      this.queue.publish('flight_booked')
    } catch (error) {
      this.queue.publish('flight_cancelled')
    }
  }
  cancelFlight(msg) {
    return this.repository.cancelFlight(msg.flightId)
  }
}
```
