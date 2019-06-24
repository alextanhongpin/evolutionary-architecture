## Istio/Conduit/Linkerd/Nomad

Learn the basics of Istio and Conduit and create examples. Also look back into Nomad and Linkerd.


## Cloud Native apps with microservices architecture

- Building and Deploying a Microservice 
- Discovery and Invocation 
- Microservices Patterns 
- Circuit Breakers 
- Pipelines 
- Authentication 
- Logging, Monitoring, and Tracing 
- Blue-Green Deployment 
- Canary 
- Moving from Monolith to Microservices 



## Latency Issue Debugging

check cpu, memory usage, allocations, goroutines stack trace

Request tracing tools
- nettrace, openzipkin, `opencensus`


## ChatOps

Deploy with Slack commands, for `dev` and `stage` anyone can deploy, for `prod` need consensus from at least 2 person:

```
\deploy app-name staging
```

## How to perform traffic multliplexer

Rather than performing blue-green in production, would it be better to multiplex the traffic to both production and staging environment. This reduces the effort to manually test the staging environment. Also, is it possible to rollback the state (time-travel) if there are errors in order to have a clean state. Of course, the danger of duplicating the data is also security related. 

## OpenTracing nginx integration

## Implement SRE practices


## TODOS

- Learn nats/redis/kafka
- Learn Postgres
- Learn Kubernetes, Consul, Nomad, Terraform, Prometheus
- Learn envoy, and all the functionalities, with docker, kubernetes, rate limiting and then setup with grpc mysql redis etc. Learn back linkerd tutorial.

## Kubernetes service to service discovery
- https://kubernetes.io/docs/concepts/services-networking/service/
- https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/#exposing-pods-to-the-cluster



## Monitoring/Observability

Relook into prometheus and statsd
- how to scale statsd 
- where to store data into statsd (graphite), how to stream data to graphite and how to get data out from graphite 
- setup docker version
- setup kubernetes version
- setup ui for monitoring (grafana)
- how to perform alerting and detect threshold
- how to get realtime statistics

Relook into logging with fluentd
- how to detect patterns with fluentd
- how to optimize logging
- how to send the logs to aws s3
- what tools can be used to visualize large logs data (write a custom exporter from s3 to elasticsearch)

Relook into search engine with bleeve and elasticsearch kibana etc

Tips on scaling
- scaling is about managing resources. In order to scale, we need to know the capacity of our system. In order to know the capacity of our system (request per second, cpu, memory etc), we need to measure it. Instead of relying on the system to support the given capacity that we want, we can define it ourselves by limiting them (setting the requests per second by adding rate limiter, limiting the cpu and memory etc). This allows us to have better control over the system. We can load test to determine the capacity of the system upfront. 
- if the code works, then the infrastructure is the one likely to fail(db)
- to reduce resources overhead/consumption, use caching/snapshotting. Handle the requests asynchronously, and let the client ask for the response. If the requests have not yet been processed, then we can just return a different status to indicate that it is pending processing. We can return the stale response when the system is still carrying out the processing. 
- Some killers includes sorting the whole table, or getting the total count. Joins can be a killer too when the database are sharded
- snapshot expensive operation (count) etc and periodically update them
- reduce thundering herd and hotspot keys by throttling the requests
- rate limit client apis and calls to database
- use singleflight/groupcache for caching/preventing deduplication of requests
- circuit breaker etc/ retry/ratelimit/throttling
- use asynchronous handling for expensive job
- instead of letting the client make too many requests, we can stream the data back to the client side instead, using websocker or server sent events
- the rule to caching is to determine the lifespan of the data. Is it static? then we can cache it longer. Is it dynamic, then the caching duration will be shorter. If we want real-time data, we can also look into the push approach instead of the pull approach. This could be better in some ways. 

## Deployment

how to handle zero downtime deployment

https://www.ebayinc.com/stories/blogs/tech/zero-downtime-instant-deployment-and-rollback/
https://www.exoscale.com/syslog/kubernetes-zero-downtime-deployment/
https://blog.pvincent.io/2018/12/patterns-for-zero-downtime-deployments-of-large-applications/

## Consul envoy service discovery
