# Testing in Evolutionary Architecture

Testing should be part of every architecture. The architecture role is just to define a structure, but not a rigid one that it becomes too accomodate changes, nor too generic that makes the architecture fragile. A good architecture should support the business, which is the domain model.

Since business rules changes (new requirements added, values changes - note: write about defining/measuring values), the test will change along with it.

Good tests however does not touch the implementation detail - it should only test the behaviour of the application. What's the definition of the application behaviour? Simple. The change in state when an input is provided, which returns an output. Treat the process/behaviour in the middle as the black box - we do not need to know how (the technology, implementation details) on how the input results to the output. We just need to know it changes, and we can capture and prove the changes is what we desired. A good way of capturing this changes is `Specification by Example`.

```
Given [an initial state, input]
When [something is done, black box]
Then [a result is produced, output]
```


## What to test?

- the success scenario test. Write tests that demonstrates the success scenario for your service implementation. This also serves as a documentation for users to know the right usage for your service.
- the null test. Write tests to check the condition when no value is pass in service
- the undeterministic test (fuzzy). Use random data to test the service logic.
- the permission/role/scoped test. Can a user (e.g. non-admin, non-owner) perform the action?

## Testing with Database

  Should I test against a database?

Testing with database is normally not recommended. But since it is easy to start a local docker development, this makes it easier to run integration testing against the database. This is also useful, since a lot of errors originates from the wrong sql query.


## Testing and Different methodology

There are TDD, BDD. There's also `should`, `assert` terms used when testing. UseCase requirements can be converted to BDD (GWT, Given When Then style). The problem is the test case tends to get long. Testing a pipeline service can be hard to setup, since there's a possibility the scenario may branch out, causing repetitive steps to be performed.


## Testing API HTTP requests

Testing API HTTP request is probably something I've seen many developers doing. While it's not wrong, it doesn't give much details about what we are actually testing. When making a HTTP API requests, the system becomes a black box - we don't really care about the business logic that they are performing, only whether they are returning the correct response payload. Also, the payload could varies, which leads developers to hardcode the json response, again defeating the purpose of the testing.

It is better to isolate the business logic into a Service layer, and call them in the Controller layer. For testing, we can just test against the Service layer rather than the Controller layer.

Testing becomes incredibly straightforward too, since we can bypass the middlewares and loggers, and we can also setup our own infra and pass them down through dependency injection. Some might argue that middlewares are part of business logic too (authentication/authorization), but again, we are not advocating against testing againt API requests - there's a place and time for it.

Testing API requests is common if we are testing against a client API/external integration. For that, it's a better to look into PACT contract testing.
