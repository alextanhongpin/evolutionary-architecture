designing a code structure for nodejs

how do you design a microservice architecture
- graceful shutdown
- error handling
- graceful background process/worker termination
- signalling closing
- secure headers
- ratelimiting
- retries 
- circuit breaker
- logging
- synchronous - request/response
- asynchronous - message queue
- service pattern
- repository pattern
- mvc pattern
- onion architecture
- request id correlation
- distributed tracing
- metrics collection
- service discovery
- load balancing
- schema validation
- database migration
- deployment
- build server strategy
- dockerization
- app sensor
- health endpoint

Entity vs Data Transfer Object (DTO)
- only rules that concern that particular object
- usually implemented in OOP style

Use cases
- business rules of the specific application

E.g.
Gather info for new loan

Input: Name, address, birthdate, etc
Output: Same info + credit score

Rules:
1. Validate name
2. validate address
3. get credit score
4. if credit score < 500: activate denial
5. else create customer (entity) and activate loan estimation


Principles in implementing clean architecture

SOLID
- Single responsibility principle (SRP)
- open closed principle (OCP)
- liskov substitution principle (LSP)
- interface segregation principle (ISP)
- dependency injection principles (DIP)

Others
- Reuse/Release equivalence principle (REP)
- common closure principle (CCP)
- Common reuse principle (CRP)
- Acylic dependency principle (ADP)
- Stable dependency principle (SDP)
- stable abstraction principle (SAP)

https://pusher.com/tutorials/clean-architecture-introduction



TODO: Create a dependency graph on the architecture that we currently have. Show them the sequence diagram on how a call works.

