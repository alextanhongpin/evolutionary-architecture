## JSON Schema

Use JSON schema to build reusable schemas and host them in a central server. The schemas can be used on both the client (not necessarily frontend, can be another server) and server too as validation. You can also use the schema to generate reusable forms on the frontend.

## Typical mistakes

- `start_date` and `end_date` is not properly formatted. In the case where both dates are the same, the difference will result in 0, and might cause mistakes in calculation involving days. It is always a good practice to set the start date as `00:00:00` and end date as `23:59:59`, and round up the difference.
- whitespaces. trim the strings to ensure the length is valid.
- orientation for different languages (left to right and vice versa).
- emoji in database. For mysql, set the character set to utf8mb4
- payment record. Store the final pricing in a table once the item has been paid. Do not attempt to recalculate the values from scratch, because the values will change (product price, discounts etc). Always store the final state.

## Standard Format

- email (go find out)
- url (can use the length of 2083 in mysql, and also suggest the usage of url shortener for client)
- date (store as UTC, and RFC3339)
- password (see password hashing, PHC format algorithm)

## Validation on Server-side API and Client side

APIs are easy to design, think of an api service as a black box that takes in a request, does some work in between and returns an appropriate response. From here, we can have several variation on what could possible happen:

- valid request - users will get a valid response in return
- requests with additional fields - server should remove those additional fields
- extra large requests - same as above
- requests with duplicated fields - only take the last field
- requests with invalid data type - validation errors should be returned. Additionally, the server should capture the request alongside the error and log it for debugging

Given a _valid_ requests, the server is bound to return a _valid_ response. With that said, it's important for the server to handle the validation of the requests that is sent. It is not safe to assume that the client will always send you a valid requests, because:

- requirement might change, and the client/server might not be updated
- the requests can come from other external sources, hence it is imporant to protect the endpoint
- if the endpoint is not idempotent, multiple trigger of the same endpoint can corrupt data

The good thing is, it is obvious where the validation should be done - preferably the first layer before invoking the `service` that resides in the `controller`. Every `controller` (which is the endpoint) will have __one__ service inside of them. To illustrate an example with `node.js express` (it will be the same with other languages):

```js
const router = require('express').Router()
const logger = require('bunyan').createLogger({
  name: 'car-service'
})

const CarController = {
  
  postCar(req, res) {
    const ctx = {
      requestId: uuid()
    }
    let log
    try {
      // Build a context with the request id - this request id will propagate through all the functions 
      // that is called within the service. This enables us to "trace" all the steps including the steps 
      // that is causing the error. The id is unique for each request. The `requestId` is returned as 
      // an error_code in the error response, and can be used to debug the error. The error_code can also
      // be used to submit a `crash_report` on the frontend, so that when user's submit the form, the devs
      // knows what to look for.
      
      // Build the request. Here we are being selective on the fields that we want to include. 
      // This is good, because users can simply pass in additional fields. Also, it makes the 
      // endpoint more explicit, since we know what is required for the service to work.
      const request = {
        // This can be placed in the context, if we want to validate the request as a single entity.
        id: req.params.id, 
        // This is the user's id that is obtained from the auth middleware before calling this function.
        user: res.locals.id, 
        payload: req.body
      }
      // Build a logger that includes the request id, and the client requests. When an error occured,
      // we can get the request id and also the request that the user sent to find out what happened.
      log = logger.child({...ctx, request})
      
      // Calls the actual service. The decoupling of the service is pretty useful.
      // When doing unit testing, I just need to test the service, and not the endpoint. 
      // If the endpoint contains a middleware for auth, it can be easily skipped too.
      // Actual validation of the request is done in the service layer, since they should be responsible
      // for the models and business logic.
      const response = await carService.postCar(ctx, request)
      
      // The service should return a valid response. Additionally, the response can be parsed
      // here before returned.
      return res.status(200).json({
        data: response
      })
    } catch (error) {
      // An error occured, log the request id and request, and error.
      log.error({error: error.message}, 'getCarError')
      return res.status(400).json({
        error: error.message,
        error_code: ctx.requestId
      }) 
    }
  },
  getCar(req, res) {
    // GET endpoints are pretty much simplified - they don't have much business rules and are idempotent,
    // and hence logging the errors will only add more noise to the endpoint. Endpoints with the side-effects
    // (mutates data in db, calling external services that could fail) are the ones that requires special attention.
    try {
      const ctx = {}
      const request = {
        id: req.params.id
      }
      const response = await carService.getCar(ctx, request)
      return res.status(200).json(response)
    } catch (error) {
      return res.status(400).json({
        error: error.message
      }) 
    }
  }
}
router.get('/v1/cars', CarController.getCar)
router.post('/v1/cars', bearerAuth, CarController.postCar)
```

The validation rules resides in the services and models instead. The server-side models and client-side models are similar, which means that the validation rules can be shared. The client has more handling for the errors, since they might need to display it based on the user's locale (multilingual), and also warn user right when user enter the wrong information in the forms. 

Sharing models is possible if both languages are the same on the server or client side (in go, we can expose the models as a package). Hence, a language-agnostic solution like __JSON Schema__ actually is more favorable, since it doesn't concern about the languages used and there is a module for different language.


## Naming convention

`validate` or `verify` should return error. `check` should return bool.
