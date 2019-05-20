# Orchestration based saga

https://microservices.io/patterns/data/saga.html

```js
class CreateOrderSaga {
  constructor(queue) {
    this.queue.subscribe('APPROVE_ORDER', this.approveOrder)
    this.queue.subscribe('REJECT_ORDER', this.rejectOrder)
  }
  approveOrder() {
    // Update order to approved.
  }
  rejectOrder() {
    // Update order to rejected, sends the message to the previous action to compensate.
  }
  async reserverCredit() {
    this.queue.publish({
      event: 'RESERVE_CREDIT',
      data: {}
    })
  }
}

class OrderService {
  constructor(saga = new CreateOrderSaga()) {
    this.saga = saga
  }
  createOrder(orderId) {
    // Sends the message queue to the Customer Service.
    // Runs in background.
    this.reserveCredit()
  }
}

class CustomerService {
  constructor(queue) {
    this.queue = queue
    // From the order service saga.
    this.queue.subscribe('RESERVE_CREDIT', this.reserveCredit)
  }
  reserveCredit() {
    // try ...
    const success = true
    if (success) {
      this.queue.publish({
        name: 'APPROVE_ORDER'
      })
    } else {
      this.queue.publish({
        name: 'REJECT_ORDER'
      })
    }
  }
}
```
