There are many layers where we can write business logic, and each has their own pros and cons.

- data layer (plpgsql)
- application layer
- client (mobile or web, but best to avoid)
- external (load balancer, sidecar)


The deeper the layer, the more consistent and strict the business logic will be. Moving outwards the logic will be more specific to the client. 

In short, if we have a business logic that needs to be enforced for all, we should strive to place it in the deepest layer. 

This has the implications of coupling the behaviour to the data layer, which could be an issue for maintainability as well as flexibility.
