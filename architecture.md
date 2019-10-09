## What are good code architecture

- prevent circular dependency
- logical layers 
- business logic layer is clear
- each layer only have a reason to change
- business logic in the model
- the disadvantage of lazy loading the dependencies is that we don’t know if it works until it is triggered. Hence, it is best to have a function that initialises the critical dependencies to ensure they don’t break when they are called.
- lazy loading have the advantage of initialising the dependencies that are needed, only during that time.
- sensible naming convention, e.g. verb+noun for services/repositories, method+service name for controllers, noun+verb+past participant


## What are the different kind of architectures available

Different architecture and how they are suitable


- Saga design pattern 
- Cqrs
- Event sourcing
- Mvc
- Onion architecture
- Clean code

How would each of them work?


## AOP
Aspect-oriented programming, and how it could be use to modularize functionalities. Stuff like decorators for throttling etc.

## Entity component system

http://vasir.net/blog/game-development/how-to-build-entity-component-system-in-javascript
https://en.m.wikipedia.org/wiki/Entity_component_system
https://mmstick.keybase.pub/managing-memory-in-rust-ecs/

## References

Good articles to read
- https://www.aosabook.org/en/index.html


- https://www.tutorialspoint.com/software_architecture_design/distributed_architecture
- https://keetmalin.wixsite.com/keetmalin/single-post/2017/09/27/Distributed-System-Architectures-and-Architectural-Styles
- https://slack.engineering/scaling-slacks-job-queue-687222e9d100?gi=14c80a524901
- https://en.wikipedia.org/wiki/Architectural_pattern
