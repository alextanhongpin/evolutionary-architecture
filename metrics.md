# Metrics


- how to we deal with metrics gathering and collection?
- how do we track events from client side?
- how do we build a pipeline to process client side events?
- how do we handle errors from client side?
- how do we gather user actions from the client side and link them back to the user (using client ip will not work, since many users can share the same ip)

## Tools

- https://opentelemetry.io/
- Open metrics
- Open tracing
- Open census
- prometheus


## Identifying values in services

We may deploy hundred services, but how do we define the values of those services? Often, we question ourselves on how to write microservices, what are the boundaries of the services, why should it be designed that way, but we overlook the most important question - what is the value of a service?

How do we measure the value of the service? The most naive one is through the usage. Consider the following:

```
Services    Calls/day      Availability
Service A   100000         99%
Service B   1000           98%
Service C   10             100%
```

Here we see that service C has the least call. If a service does not deliver value, it is wise to remove it.
For services that consumes a lot of call, we can probably segregate the data by users to see who calls it more often - this can lead to more interesting discovery like why the user calls the service often, and what can be done to improve the service (custom-tailor microservices).


## How to design a pluggable metrics engine?

What is the metrics engine for? 
- monitoring
- detecting performance bottleneck
- plan to scale
