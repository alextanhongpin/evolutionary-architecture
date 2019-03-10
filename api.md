
- capabilities url
- default action, CRUD 
- custom action, search, approve etc
- filter, search, query
- params or query? `/users/:id` returns specific user by id. If we need to query user's by email, either make the id or email conditional (but exposing the url in the query is not a good idea, so...) or use a querystring for `/users?email=john@mail.com`.
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
