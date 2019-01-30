# Dependency Injection (DI) and Inversion of Control (IOC)

## EventEmitter

Other than the typical way of passing down dependencies, we can also use EventEmitter to register them externally when needed. Consider a `PurchaseService` that will send a Slack notification whenever a new purchase has been made:

```js
class PurchaseService {
  constructor(slackClient) {
    this.slackClient = slackClient
  }
  purchase(req) {
     // Simplified...
     const product = await repo.create()
     await this.slackClient.notify({ message: 'Purchase Made', product })
     return product
  }
}
```

We are introducing a new dependency (`slackClient`) to our `PurchaseService`, hence coupling them. Note that the implementation might change - we might resort to sending emails instead of Slack notifications, or maybe even both, or none! Also, we have introduced a side-effect to our code - the delivery will be triggered whenever the purchse is called.

In order to avoid triggering the side-effect, we have to mock the `slackClient` and introduce a `nop` (Null Object Pattern), or just mock the methods.

How can we do better? Consider the implementation below:

```js
const EventEmitter = require('events')

export const PURCHASE_MADE = 'purchase_made'

class PurchaseService extends EventEmitter {
  purchase(req) {
     // Simplified...
     const product = await repo.create()
     this.emit(PURCHASE_MADE, product) 
     return product
  }
}
```

Here we are extending our `PurchaseService` class with an EventEmitter. We have made several improvements:
- this code is now free from an extra dependency
- there are no longer any side-effects
- testing can made simpler by registering a handle to check the output of the emitted events
- we can register multiple handlers for the events
- failure in sending any of them will not affect the main logic

Usage:
```js
const service = new PurchaseService()
service.on(PURCHASE_MADE, (product) => {
  // Send slack notification...
})
service.on(PURCHASE_MADE, (product) => {
  // Send email notification...
})
```
