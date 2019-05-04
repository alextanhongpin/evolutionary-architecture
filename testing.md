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

## QuickCheck testing

## Testing Scenarios

- testing positive scenarios
- testing branch scenarios
- testing unexpected scenarios
- ensure each branch scenario is covered
- negative testing (use quick check)
- generating unexpected input. most of the time, we only test with inputs we expect, or the boundaries which we recognized. It's sometimes better to generate unexpected input and test them

Fields:
- optional/required field
- combination of required/optional fields
- default fields
- data type, default data type
- null
- dependency between different fields (end date cannot be earlier than start date)

String:
- empty string
- empty spaces
- string with spaces in front or behind (not trimmed)
- string with newlines
- unicode issue, or handling or other languages 
- left to right or right to left issue
- case sensitive? capitalization matters
- min, max characters. Password should not have length less than 9...more than 2000 (?)


Int:
- whether it is an int or float (can pagination be float? nope)
- min max boundary (for product in stocks, we cannot choose Infinite, or any value greater than the stocks we have, age cannot be 0)
- infinite values
- negative vs absolute value
- is zero allowed (age cannot be 0)
- default values
- is negative allowed (use uint), e.g. selecting -tive product quota/pagination

Date:
- string date can add confusion (1/2/1990 can mean both 1 February 1990 or 2 January 1990 if we do not specify the format). User may also enter the wrong format if they are confused). Solution: store timestamp, or RFC3339 date. Present it differently on the frontend (1 Jan 1990)
- end date greater than start date
- date relative to today (e.g. today's date is xxx, but when stored in server's UTC, there's no conversion and it is misleading)
- timezone issue. Products are set to expired at date A UTC, but in different region, it is not the expected 12:00 AM expiry date. This can be confusing for events etc.
- the past. Some dates should not be set in the past. E.g. Creating a calendar event in the past (?).
- the future. Some dates should not be set in the future (birthdate cannot be tomorrow). 
- overlapping date. When we upgrade a subscription, the old ones needs to be terminated (end_date), and the new one needs to be created with a start_date. If we set the end_date and the start_date to be the same, and if we create a prorated charge per day, the user will be charged twice on the day the subscription ended. Instead, the end_date should be set to yesterday, that way the user is only charged once for the old plan and once for the new plan (assuming it is a daily plan, if it's hourly, it could still be possible to charge by the difference in hours).

Behaviour:
- can it be called once/twice/multiple time (idempotency, cannot pay twice, cannot send email twice)
- side effects triggered (one state must have an action and an outcome). Sending email twice...
- one time only action
- one entity status is dependent on the other
- calling an entity action when the previous is not completed/not exist
- sequence of step not respected (step 1, 2, 3) is called step (3,2,1)
- calling in reverse sequence
- skipping sequence
- combination of skipping, calling twice etc
- double click issue (also concurrent)
- post redirect get 

Resource:
- attempting to act on a resource that does not exists
- modifying other user's resource
- acting on the same resource twice (side effects, e.g. purchase subscription twice, pay twice)
- double create
- parent child resource relationship. Deleting parent must cascade to the children - they should all be removed.
- roles attached to the resource have changed, will the relationship still be the same
- expiring resources (e.g. event may expire, but the users that booked the event may still see the expired event. User like a product on the website, but it has been removed. A deal has expired before the user purchase it, the discount is no longer valid).

Create:
- calling create twice (double submit)
- cannot create twice (idempotent, cannot pay twice)
- create with dirty data

Update:
- unsetting the values (submit empty name the second time)

Custom Entity/Alias are types that extends the basic type, or types that are used to model a domain:

- birth date: alias of date, but the date cannot be in the future (must be at least today.
- age: cannot be negative, cannot be an abnormal value (what is considered abnormal? any value that is more than 100 could rouse suspicion, but any values greater than the current year is definitely abnormal)
- gender: male, female, others. This is usually an enum type, and the capitalization should not matter, but prefable lowercase everything.
- money: can only be +tive. The signs are usually denoted by the type (income, expense)
- car: the wheels cannot be 2
