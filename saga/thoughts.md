I probably misunderstood the real-intention of saga, they are probably meant for dealing with long-living transactions, where the request/response may not be immediate. 

Just when I thought that a simple http API call will suffice:

```js
async function createOrderSaga(order) {
  try {
    await makePaymentCommand(order)
  } catch (error) {
    // Rollback and return.
  }
  try {
    // Should make it an idempotent retry.
    await makeDeliveryCommand(order)
  } catch (error) {
    // Rollback and return.
  }
  return true
}

// Note that the try catch will scope the values returned. We can do this instead (inspired by golang).

async function makePaymentCommand(cmd) {
  try {
    const res = await doSomething(cmd)
    return [res, null]
  } catch (error) {
    return [null, error]
  }
}

// A more flattened response.
async function createOrderSaga(order) {
  const [res, err] = await makePaymentCommand(order)
  if (err) {
    /* Rollback and return. */ }
  const [res2, err] = await makeDeliveryCommand(res)
  if (err) {
    // Note that now we need to rollback both events! We can do that by pushing the undo command for each completed steps
    // into an array, then when an error occured, reverse the array and execute the undo commands. The undo commands
    // should not fail!
  }
  return true
}
```

But then I realised, there are many potential for errors here:
- makeDeliveryCommand might be interrupted (current server restarts, shutdown, fatal errors, or maybe the server it is making calls to is experiencing load/internal server errors)
- restarting the server does not allow one to resume the previous operation
- one of the steps could be taking a long time (hours, days, months!) to complete. In the example above, `makeDeliveryCommand` could potentially take more than a day to process.
- 

Solutions:
- for every steps, log the steps/send the steps to the message queue and store the completed progress in a storage (event store?)
- make the steps independent from one another, not sequential as the one above suggests. Each steps should be executed in the same order, but not necessarily in the same function. Each completed steps could emit an event, which will be picked up by the listener, which will the queries the last step of the sequence and proceeds with the processing. Upon completion, there should be a signal to indicate completion.
