## Types of rule engine

- forward chaining engine
- condition action engine
- flow processing engine
- decision trees/tables
- stream processing engine
- complex event processing CEP engine
- finite state machine

## Use Cases

Can be used to model complex business rules to take actions (sending notifications), fraud detection, expert systems etc.



## Algorithms

https://en.wikipedia.org/wiki/Rete_algorithm


## Companies that works on rule engine

https://blog.waylay.io/iot-automation-forward-chaining-engines/
https://blog.waylay.io/rules-engine-for-complex-logic/
https://blog.waylay.io/waylay-engine-one-rules-engine-to-rule-them-all/
https://www.waylay.io/rules_benchmark/index.html
https://blog.waylay.io/tag/rules-engine/

For fraud detection:
- https://eng.uber.com/mastermind/
- https://blog.skymind.ai/multi-tier-system-for-detecting-fraud-with-ai-and-deep-learning/
- https://feedzai.com/blog/improving-fraud-detection-rules-versus-models/
- http://www.fraudpractice.com/gl-rulesengine.html



## designing a better rule engine

when designing rule engines, we work more with domain specific language (value objects), rather than primitives types.
- instead of string, we work with email, sku, passport number, credit card number etc, which has different validation logic
- these value objects have different boundaries too for values, e.g. age cannot be zero, or more than 200. purchase count cqnnot be more than number of stocks available
- most of the time, they have different units too (use UTC timestamp, currency should be normalized to USD etc)
- datetimes are especially tricky - we should not accept values that is passed in from the client that is from the future, for example, or from the past that exceeds a certain threshold)
- we also have to deal with computed values, such as age. Imagine a scenario where the rule does not apply for 13 year old user, but when he turns 14, then the rule applies.

Some useful tips
- set boundaries (min, max, between)
- cover all edges (empty, null, invalid, valid cases for the given value object)
- ensure there are no overlapping conditions
- normalize all units to a standard unit (timezone, currency, numbers, height etc)
- be careful of dates (they should be in timestamp utc, start date cannot be less than end date, dates cannot be from the future, date range in different timezones etc)


## What can rule engine be used for 

- cashback engine
- games
- job application
- ABAC/RBAC system
- flights pricing
