# Complex Event Processing

How can we come up with a simple prototype?

https://databricks.com/glossary/complex-event-processing
https://blog.equinix.com/blog/2018/03/28/developing-a-successful-end-to-end-complex-event-processing-strategy/


- Business rules requires facts to be gathered first. Facts are deduced by events. We can have facts on

- Something that is supposed to happen once, e.g. user selects an order
- Something that is supposed to happen twice. User calls the service twice
- Something that is supposed to happen multiple times.
We can take the statement above and apply the NOT logic. 
We can also compose multiple of those occurrences with AND, OR , WHEN, ALL, SOME etc.
We can set a TIME WINDOW, turning them into time series events.
- sequence of events is even harder. There are predecence, or concurrent. The former states that an action must occur before the next, the latter states that both can happen at the same time)

A simple rule to check the user’s home address can be based on the following facts:
- Fact 1: Location of user when the time is 2-3 am
- Fact 2: The highest frequency of the location for 1 week.
- The business logic will then be to sort the locations with the highest frequency and return them
- That concludes the rule check user home address

We can extend that to:
- Check users change address. If the same pattern is repeated above and the value differs from the previous location, then trigger the user change address rule.
- Check user’s work address. We just need to change the time to check to 10-12 am. (before lunch). There’s a probablility that the work place and the home is the same, or we can also deduce that the user might not be working. 
We can store the users temporal data in such a way we can capture the change of facts. 
- valid from
- Valid till 
- Facts
- Rule that deduce the facts 
