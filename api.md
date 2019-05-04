
- capabilities url
- default action, CRUD 
- custom action, search, approve etc
- filter, search, query
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


Other options:
- graphql
- websocket
- server sent events
- grpc

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

## 
