## Sample 1
```js
// REVIEW_SUBMITTED_EVENT: The first event. It starts our business process as well as the saga.
// REVIEW_IN_EXAMINATION_EVENT
// REVIEW_APPROVED_EVENT: The last event. This event ends our saga.
// REVIEW_REJECTED_EVENT: A compensating event. It also ends our business process, but in the way of the rollback. Deletes the rejected review.

class Saga {
  name = 'The name of the saga, e.g .ReviewCheckingSaga'
  id = 'The aggregate id'
  handler = 'A reference to the service which is called by the saga in case an event occured.'
  requiredEvents = [] // The names of all the events which make up the saga.
  rejectionEvent = 'The name of the rollback event that cancelled the saga'
  events: [] // All the events that has already occured.
}

class ReviewService() {
  constructor(queue) {
    queue.on('REVIEW_SUBMITTED_EVENT', (event) => {
      // Creates the saga on receiving the event.
      const saga = new Saga({
        name: 'ReviewCheckingSaga',
        id: event.id,
        handler: this.reviewCheckingService,
        rejection: this.rejectReview,
        requiredEvents: ['REVIEW_SUBMITTED_EVENTS', 'REVIEW_IN_EXAMINATION_EVENT', 'REVIEW_APPROVED_EVENT', 'REVIEW_REJECTED_EVENT']
      })
      sagaService.save(saga)
      // Apply the first event.
      sagaService.apply('ReviewCheckingSaga', event.id, event)
    })
  }
}

class SagaService {
  constructor(repository) {
    this.repository = repository
  }
  apply(sagaName, id, event) {
    const saga = await this.repository.get(sagaName, id)
    const handler = saga.handler
    // Invoke the handler. This should reference another method (or use the visitor pattern?).
    try {
      const _ = await handler(saga.event)
      // Publish if success.
    } catch (error) {
      // await saga.rejection
    }
    
    if (allEventsOccured(saga)) {
      // Mark as completed.
    }
  }
}
```

References:
- https://tuhrig.de/saga-pattern-with-spring-boot-and-activemq/


## Sample 2

```js
class Saga {
  constructor(opts) {
    const {
      name,
      id,
      action,
      payload
    } = opts
    this.data = opts
  }
  json() {
    return this.data
  }
  commit() {}
  rollback()
}

class SagaOrchestrator {
  constructor(repo, reviewService) {
    this.repo = repo
    this.reviewService = reviewService
  }
  apply(event) {
    // NOTE: With the bitwise trick, we can store all the events in one column together with the json data.
    const saga = await this.repo.findOne(event.name, event.id, event.action)
    switch (saga.event) {
      case 'REVIEW_APPROVE_REQUESTED':
        const result = await this.reviewService.review(saga.payload)
      default:
        return
    }
    // const await checkCompletion(event)
  }
  async checkCompletion(event) {
    const sequences = await this.repo.fetchAggregateId(event.name, event.action)
    // Validate the sequence of events.
  }
  async save(saga) {
    try {
      const res = await this.repo.createIfNotExists(saga)
    } catch (error) {
      // If error === duplicate saga...
    }
  }
}

// There are many potential area of failures here. Best is to have each step executed through a message queue with the state persisted somewhere.
const sagas = [saga1, saga2]
const completed = []
const isInterrupted = false
for (let saga of sagas) {
  const _, err = await saga.commit()
  if (!err) {
    isInterrupted = true
    break
  }
  completed.push(saga)
}
if (isInterrupted) {
  completed.reverse()
  for (let saga of completed) {
    await saga.rollback()
  }
}
```
