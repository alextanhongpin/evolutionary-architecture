# Patterns in API


The basics
- CRUD


Additional
- [capabilities url](https://www.w3.org/TR/capability-urls/)
- default action, crud
- custom action, search, approve etc
- filter, search, query, selecting fields, including relations
- params or query? `/users/:id` returns specific user by id. If we need to query user's by email, either make the id or email conditional (but exposing the url in the query is not a good idea, so...) or use a querystring for `/users?email=john@mail.com`.
- overwriting query? `?name=car&name=paper`
- pagination: use graphql style with next, prev pagination that is base64 encoded
- user roles, permissions and scopes
- x-request-id
- rate-limiting, and x-rate-limit
- caching
- validation
- private/public fields for different roles
- security (authentication, authorization, bearer, basic and custom hmac sha256)
- jwt, jwe and jws
- data type (e.g. for time, use RFC3339 string)
- http status codes
- versioning
- documentation (swagger, or custom)
- how does REST compares to GraphQL


Other options:
- graphql
- websocket
- server sent events
- grpc

## Rest vs Graphql

- Graphql will always return status 200
- Error handling in graphql is different than REST
- handling nested relations is easier than REST

## New API?

https://apievangelist.com/2015/05/05/guest-post-why-the-api-pattern-is-broken-and-how-we-can-fix-it/


## API Monetization

https://dba.stackexchange.com/questions/105915/modeling-invoices-and-orders
https://developer.ibm.com/apiconnect/2017/10/17/api-monetization-really-mean/
https://nordicapis.com/top-5-api-monetization-business-models/
https://www.quora.com/What-are-innovative-ways-to-monetize-your-API

Some other tips:
- separate frontend from backend
- application can be whitelabelled, and apis can be charged per requests

References:
- https://cloud.google.com/apis/design/
- https://jsonapi.org/
- https://json-schema.org/
- https://github.com/CiscoDevNet/api-design-guide
- https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design
- https://hackernoon.com/restful-api-designing-guidelines-the-best-practices-60e1d954e7c9
- https://hackernoon.com/restful-api-design-step-by-step-guide-2f2c9f9fcdbf
- https://apiguide.readthedocs.io/en/latest/
- https://swift.org/documentation/api-design-guidelines/


## Resource Based URL

URLs naming for Rest APIs are usually plural nouns. 

## Roles in APIs

Some of the difficulty in implementing APIs correctly is the service part. APIs are mainly a layer of HTTP transport, but the core of the APIs are usually service. When designing api services, there are times where we tend to mix up the responsibility and fallback to SOAP based approaches - using verbs. This is understandable, when the APIs themselves may have different behaviours when called by different roles - they simply did not adhere to the classic CRUD APIs.

Google recommend naming custom methods the following way:

- `POST /v1/resource:cancel`
- `GET /v1/resource:batchGet`
- `POST /v1/resource:move`
- `POST /v1/resource:search`
- `POST /v1/resource:undelete`

https://cloud.google.com/apis/design/custom_methods

## Standard Fields
Standard fields as defined by google: 

https://cloud.google.com/apis/design/standard_fields

## JSON API Specification

Look into JSON API specification and understand how to implement it correctly.

## Avro Schema

Look into avro schema registry and compare it against protobuf.


## Service Value

Defining Service value will enable easy rewrites of the API.



## System API, Experience API and Process API

https://www.mulesoft.com/sites/default/files/resource-assets/API-led-connectivity-new-soa-updated.pdf

Try to understand the separate layer and why they exist. Is it also possible to abstract, say database access calls to one layer, and reduce the side-effects by splitting read/write (CQRS), which can only be consumed through RPC by other clients?

## Exposing schemas

Given a hypothetical api endpoint `/foods` which return foods, we can have an equivalent schema endpoint `/schemas/foods` that returns the JSON Schema of the resource.

## Intelligent API as a Service

Spam, NER, Search, Viterbi, Markov Chain, Bandit, Recommendation, Sentiment Analysis API for real-world use cases.



## Service Migration

If there are new services with changing schemas, the idea is to deprecate the older version. It will still be running, but warn the end users about the deadline and the necessary changes for the newer version. Also, log the requests count together with the version to see how many people are still consuming the endpoint. Once the count reach below certain threshold, deprecate it completely. To make the migration transparent, one can apply the proxy or adapter pattern.


## Microservices Approach

- what microservices to build? video? check for existing schemas in schema.org (DRY)
- what cloud design pattern is involved? basically how to handle scaling in the future
- what technology stack to use (no debate here, but it's good to be clear on this - use the right tool)
- simple mvp and test cases
- what data needs to be analysed


## Traffic Shifting

It is possible to do traffic-shifting - but can we shard (duplicate) the calls to a staging environment instead to debug/test performance/check errors? When doing so, it is important to isolate side-effects (writes to db, sending out emails, triggering webhook etc). Find out how. 

## Single API for two stuff.

Most of the time, we have to deal with scenarios where we can handle conditional in one single api, .e.g follow. Consider the options below, which is better?

A single endpoint to update follow/unfollow status:
```
PATCH /songs/:id/follows
```

Two endpoints with a single responsiblity:
```
POST /songs/:id/follows
DELETE /songs/:id/unfollow
```

The first option requires us to return the status to indicate if the user has successfully followed or unfollowed a song.
The second is easier, since we know which endpoint we are calling. There operation is idempotent too - triggering it multiple times will not cause error - you can only follow once, or unfollow once.


## Custom Methods
https://cloud.google.com/apis/design/custom_methods


