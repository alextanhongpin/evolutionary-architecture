# Evolutionary Architecture

Collection of best practices when designing an server-side and client-side architecture, microservices etc.
I have always wanted to design something called the __Perfect Architecture__, and tried many different ways to implement it. But in the end I realised there's no one single perfect architecture for every scenario - the architecture depends on what you are actually building. Also, with that narrow mindset, I have end up creating a more complicated layered architecture when it can all be achieved with basic function calls. It's exactly what they coined the [XY Problem](http://xyproblem.info/). I was trying to fit something unnatural into the _patterns_ I've designed, and it kinda leaves a bad taste in the mouth. I think it's natural to try to _standardize_ the codebase and make everything look the same. As they say, _when you have a hammer everything looks like a nail_. But this approach is wrong, throughout the journey I learn to make exceptions.

__Evolutionary architecture__ is just a change of mindset. Rather than starting a project with a solid architecture in mind (which is bad), just write enough code in a single file to get things working, and then refactor as the project evolves. It takes discipline to maintain and refactor the code base, but making minimal changes to improve the architecture is indeed effective, as it avoid overengineering or even underengineering. The only requirement again is just discipline. 

## Table of Content

- Domain Specific
  - Omni-Channel Delivery Engine
  - [Engineers.my Event Page](https://github.com/alextanhongpin/go-grpc-event)
  - [go-hook, a Webhook Server](https://github.com/alextanhongpin/go-hook)
  - [go-chat, a chat server](https://github.com/alextanhongpin/go-chat)
  - [url shortener](https://github.com/alextanhongpin/url-shortener)
  - [omni-channel notification delivery](https://github.com/alextanhongpin/go-delivery)
  - [appsensor](https://github.com/alextanhongpin/go-appsensor)
  - [pastebin](https://github.com/alextanhongpin/pastebin)
  - [stock app](https://github.com/alextanhongpin/stock-sg)
  - [finanz-v2](https://github.com/alextanhongpin/finanz-v2)
  - [asteroid game typescript](https://github.com/alextanhongpin/typescript-asteroid-remake)
  - [go-openid](https://github.com/alextanhongpin/go-openid)
  - [book-my-show](https://github.com/alextanhongpin/book-my-show)
  - [chat-playground](https://github.com/alextanhongpin/chat-playground)
- Language
  - [NodeJS](https://github.com/alextanhongpin/node-rest)
  - [go-microservice](https://github.com/alextanhongpin/go-microservice)
  - [go-learn](https://github.com/alextanhongpin/go-learn)
  - [js-learn](https://github.com/alextanhongpin/js-learn)
  - [cheat-sheet](https://github.com/alextanhongpin/cheat-sheet)
- Architecture
  - [go-microservice-architecture](https://github.com/alextanhongpin/go-microservice-architecture)
  - [kubernetes](https://github.com/alextanhongpin/kubernetes-structure)
  - [database design](https://github.com/alextanhongpin/database-design)
  - [typescript-node-clean-architecture](https://github.com/alextanhongpin/typescript-node-clean-architecture)
  - [python-tornado](https://github.com/alextanhongpin/python-tornado)
  - [go-websocket](https://github.com/alextanhongpin/go-websocket)
  - [nim-jester](https://github.com/alextanhongpin/nim-jester)
  - [react-hook-boilerplate](https://github.com/alextanhongpin/react-hook-boilerplate)
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
- Flick's Ticket Server: http://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/
- How Firebase generate a unique ID: https://firebase.googleblog.com/2015/02/the-2120-ways-to-ensure-unique_68.html

## Compressing UUID to Binary

Motivation: We want to compress UUID to binary in order to save the storage size (?) and also improve performance (?).

References:
- https://mysqlserverteam.com/storing-uuid-values-in-mysql-tables/
- https://stackoverflow.com/questions/28251144/inserting-and-selecting-uuids-as-binary16
- https://www.percona.com/blog/2007/03/13/to-uuid-or-not-to-uuid/
- https://news.ycombinator.com/item?id=14523523

## System Design Interviews

References:
- https://www.quora.com/How-would-you-design-a-large-system-like-Facebook
- https://www.quora.com/How-do-you-answer-the-interview-question-How-would-you-design-and-scale-a-platform-like-Twitter
- https://github.com/checkcheckzz/system-design-interview
- https://medium.freecodecamp.org/how-to-system-design-dda63ed27e26
- https://hackernoon.com/top-10-system-design-interview-questions-for-software-engineers-8561290f0444

## References

- https://medium.com/pinterest-engineering/building-a-dynamic-and-responsive-pinterest-7d410e99f0a9

## Projects

- implementing scalable real-time chat/file transfer etc
- notification delivery
- file p2p
- scraper + indexer with pagerank
- live news feed
- recommendation engine for github, stocks etc
- routing problem (searching cheapest fares for flight etc)
- twitter/instagram/fb/uber/spotify/youtube
- streaming architecture like netflix
- photo serving service
- facebook's safety check feature

## APIs

- design sample apis for financial stuff
- design sample api for music player
- design sample api for notification delivery

## Generating Graphviz

```bash
$ dot -Tpng delivery-load-balance.dot -o delivery-load-balanced.png
```
