# Service Design

How can we design a system to focus on the business model only?

Considerations:
- isolate cross-cutting concerns (using sidecar etc)
- promote reuse of similar functionality outside the application
- ensure there is a clear boundary between storage layer, data layer and application layer
- most applications will have a storage layer, and so we can design it in a way where we can autogenerate code from storage layer, e.g. sql

Example
- user defined an entity Car, with name and year manufactured first
- the code generated should allow user to perform basic CRUD
- user can then focus on logic to populate the data for CRUD

Thoughts:
- how does it works for data with relations?
- some entity, such as `user` will have a lot of relations
