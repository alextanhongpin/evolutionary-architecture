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
