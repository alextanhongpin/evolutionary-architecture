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
  - [passport](https://github.com/alextanhongpin/passport) simplify login for microservices
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
  - [passwordless](https://github.com/alextanhongpin/passwordless)
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
- Design an architecture for a webhook server and integrate it successfully.
- Storing data in IPFS. Look into how to create DAP (decentralized application). 
- Also look into Whisper. Try to implement a simple project with it.

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
- natural disaster notification system
- webhook dispatcher system
- policy agent
- activity feed logs
- real-time location services
- real-time services
- approval system (auto/manual)
- saga orchestrator
- designing facebook like feature
- scalable counter system
- tagging system in comments/posts/feeds
- adding attachments/emoji etc
- designing group purchase forms
- sync online offline content
- multilingual and localization with i18n and i10n
- internal weekly tech newsletter for developers
- event page for tech events
- designing a searchable knowledge base for engineers
- look into websub and how to implement it
- how to warm cache strategy
- sharing data strategy 
- how to shard to scale
- how to parallelise operations (map-reduce?)

## Others

- Finanz Revamp, Residenz, Instagram clone, Facebook, what is X is Y (what if JobStreet is Facebook?). 
- Point-of-Sale system for food ordering, lorry tracking system. 
- A clone of Journal.
- Scrape github user's data from malaysia and perform a job search/developer search based on personality and specialization. Write your own recommendation engine and get feedback from users whether it's relevant or not, and perform learning from there.
- future of Web Apps - how would the future of web apps look like? Design conceptual websites with modern technologies.
- Decentralized Applications. Check out how to create and deploy a simple dapp. Also, check out solutions for distributed databases. 
- Make an app like Yik Yak, that will messages that will expire after certain days. Also, add sentiment analysis to create postive text rather than negative.
- Etherscan Viewer. Create a viewer like etherscan that caches the data in redis to speed up read. It will also
  - display transactions/blocks
  - search function
  - filter function
  - sort function
- Signal. See how to implement encryption in applications: https://github.com/signalapp/libsignal-protocol-javascript
- designing a ci server
- designing a notification system
- todo- learn lru cache, lfu cache
- learn system diagrams
- learn sequence diagram (how to implement it in your work?)
- learn use cases (how to implement it in your work?)
- design systems (how to solve scalability issues?)
- learning dask python for machine learning
- how to build a machine learning pipeline
- how to build an etl pipeline
- how to build an intelligent pipeline for processing documents (Natural language processing pipeline)

## APIs

- design sample apis for financial stuff
- design sample api for music player
- design sample api for notification delivery
- design reusable machine learning apis
- design reusable data mining api
- design reusable NLP apis
- design search api

## Architecture

Implement all the different architecture patterns

- workflow approval
- ETL
- publish/subscribe
- event driven
- cqrs
- streaming
- clean architecture
- onion architecture
- mcv and the mv* family

https://en.wikipedia.org/wiki/Architectural_pattern

## Generating Graphviz

```bash
$ dot -Tpng delivery-load-balance.dot -o delivery-load-balanced.png
```


## Questions

- how to design complex event processing CEP architecture
- offline first architecture for mobile allows us to use the application without being online. Files, data can be stored locally, their md5 hash can be compared with the server and new ones can be refetched only when the data changed. We can keep track of the state by keeping track of the hash of the data (stored as a md5 binary column in mysql?) and the timestamp of last sync.
- how to design offline mobile experience. We can cache the GET requests and stored them locally, and perform a sync refetch when we are online back. For actions that could modify state (create, update), we can store them in a queue locally, but resume them (rate limited to avoid spam, or just limit the number of items in the queue, and when the queue is full, say has 5 items, tell the user that they cannot execute the operation).
- how to design large scale multilingual system for mobile (remember, mobile deployement is cumbersome - we don't want to be submitting the app for review everytime the translations change or if there are errors. The translation file must be stored somewhere on the server which cannot be down, and a copy should be stored on the client side, cached. The translation must be versioned, since old users might not upgrade the text, and the old translations need to persist. Translation for ios, android, web must be consistent, could be duplicated to avoid breaking dependencies)
- how to handle actions for events (?) using real-time streaming architecture with complex event processing to handle business logic.
- how to update machine learning model real-time (?) or batch update (per hour, per day) when new data is feed into the system. Upgrade should only be done if the newer model is better than the older one, so we can actually run another machine to train the newer data with the previous snapshot, compare the scores and replace (load balance) the old model with the newer one.
- how to sign out all users with jwt token. You need session, and three variables, last logout at, last login at and user id stored in a distributed cache.
- how to store real-time user analytics? For every service that provides value, push the event to an aggregator that will flush the data after a certain threshold (size, requests) or time to a storage (minio, s3).
- how to get count of billions of unique items
- how to prevent users from executing the same action twice (like/unlike, same user should not be able to like more than once, and a user that has not liked an event cannot unlike it, leading to negative count)
- how to create a sharable form with capability url that will expire, same as honestbee group purchase
- how does sharding/partitioning works?
- how to partition by uuid?
- how to design distributed cache
- designing architecture with 3 levels (general, domain specific/personalized, modular). Most architecture/system design is geared towards the requirements they need to deliver. We can make it general, but for the best performance, we still need to be specific.

## SASS

"Servicify" everything that you have done, with Kubernetes/Nomad as the scheduler. Include proper unit tests for each services too, and best practices.


## How does uber payment works?

User book a cab, but the payment is done after the user arrive at the destination. What happens if the user disconnect (intentionally or not) halfway during the trip?

Implementing dynamic pricing is also possible. 

## Reverse Engineering

Why does YouTube pause the video play after a while? Maybe because they have complex event processing to determine the users activity - active, idle, picky(when they click another link before the first video payback is completed) etc.

Why does Facebook like still works when it’s offline? Because they debounce it. There’s no merit in showing real time like counter since it can be taxing fro performance. Thus it is better to only make the call after user is idle, his also prevents abuse on the system.

Why does grab shows finding for driver? It’s basically an asynchronous process working to match the drivers in the background based on the current location, whether the driver has accepted the request, or whether the driver has sufficient rating to be recommended. Note that it’s not necessary the first driver that would be selected. A bunch of drivers can first accept the passenger, then the algorithm will decide if they can take the passenger based on the rating. 


Why does Facebook implement reaction into the comments instead of like? The like allows them to find what content is popular. Reaction however, kills two birds with a stone. First, the sum of all the reactions is similar to the like. In addition, they can also grab the user's sentiment for sentiment analysis.


## IDM (Internet Downloader Manager)

Create a basic version of internet downloader manager.


## Using client processing power

Find ways to make client do more work and send statistics to the server rather than performing the job on the server side. This delegation allows the work to scale across and the server is only responsible for validating and storing the data they received from the client.

Check the implementation of [hashcash](https://en.wikipedia.org/wiki/Hashcash) and see how to implement a basic one.


## p2p 

design a p2p network for synchorization of data. It requires the capabilities to detect new peers/drop existing peers, and searching for the best peer to sync data. Also, malicious peers should be dropped.

- p2p distribution of data is a little complicated. There's no way to ensure equal distribution of data among the nodes. Consider the following - what happens when there are no nodes? There are no source of data. What happens when we set a restriction so that only one node can process x amount of data. It might work for reasonable size of node, but when there is only one node left, it will get locked from sending the data. If there are too many, the peers will be busy looking for other nodes to check if they can provide the data. 
- also, the data transferred/synced between peers must be immutable. Consider the case where the data is constantly changing - then the peer have to resync it all the time. It can still work if the data is small, private and personal to a user (think Dropbox), but for data that is shared publicly, it should be immutable.
- nodes are divided into probably a few categories - those that are static/trusted or those that are publicly run by users. Users can run peer nodes in order to reduce the load of the server. But there has to be some rewards associated with the work they have done (incentives) in order to encourage them to continuosly host them. Also, where is the single source of the data (golden record), because if the peers delete the data then the data can only be served through the static host - which also means that the static host can serve anything. Are there any way to distribute data permanently in the web?

## NewsFeed 3D
Build 3D overlapping news feed where new news will just pop on top of older ones. When selected, it will change into the standard list views.

## Write use cases
- take a look at old projects (chat, websocket, social media etc) and write some use cases
- write use cases for passwordless project
- also take time to write an implementatio for server sent events and websockets with the background task worker with different languages
- look into how to integrate background notification for websocket events

## How to design slug history friendly Id
## How to design tagging database systems
## How to design mentions
## Passwordless Login
## Create a portfolio to showcase all the apps you build
## Company Site

## API Code Generation

- Client side call
- Backend side call


## Links

https://emacsway.github.io/en/service-layer/
https://mlflow.org/docs/latest/tracking.html#concepts



