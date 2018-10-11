# Evolutionary Architecture

Collection of best practices when designing an server-side and client-side architecture

## Table of Content

- Domain Specific
  - Omni-Channel Delivery Engine
  - [Engineers.my Event Page](https://github.com/alextanhongpin/go-grpc-event)
  - [go-hook, a Webhook Server](https://github.com/alextanhongpin/go-hook)
- Language
  - [NodeJS](https://github.com/alextanhongpin/node-rest)
- Machine Learning
  - [Bandit Server](https://github.com/alextanhongpin/go-bandit-server)
  - [Spam API](https://github.com/alextanhongpin/spam-api)


## TODO

- add examples on other machine learning api
- add CI/CD examples too
- add big data architecture
- add more microservices design pattern
- add devops stuff like load balancer, monitoring, circuit breaker
- add database related stuff
- add event sourcing
- add real-time analytics stream

## How to generate unique IDs in a distributed system

Motivation: There are several advantages of generating unique id. Auto-incrementing the ids would not work in distributed system would not work due to collision in the ids. Having a unique id would also allow us to have better control over the accesses - e.g. the same id cannot be reused, or could be blacklisted. It is common to have a service that will generate this unique id, rather than each node creating their own ids, which could lead to id collision.

References:
- https://www.callicoder.com/distributed-unique-id-sequence-number-generator/
- https://medium.com/@varuntayal/what-does-it-take-to-generate-cluster-wide-unique-ids-in-a-distributed-system-d505b9eaa46e
- https://preparingforcodinginterview.wordpress.com/2017/03/21/unique-id-generation-in-distributed-systems/
- https://www.quora.com/How-can-we-design-a-unique-number-generator-in-distributed-system-without-using-3rd-party-services-existing-libraries
- https://www.slideshare.net/davegardnerisme/unique-id-generation-in-distributed-systems
- https://www.ctl.io/developers/blog/post/server-generated-keys-unique-ids-for-distributed-databases
- http://antirez.com/news/99


## Generating Graphviz

```bash
$ dot -Tpng delivery-load-balance.dot -o delivery-load-balanced.png
```
