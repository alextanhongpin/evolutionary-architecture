## What are good code architecture

- prevent circular dependency
- logical layers 
- business logic layer is clear
- each layer only have a reason to change
- business logic in the model
- the disadvantage of lazy loading the dependencies is that we don’t know if it works until it is triggered. Hence, it is best to have a function that initialises the critical dependencies to ensure they don’t break when they are called.
- lazy loading have the advantage of initialising the dependencies that are needed, only during that time.
- sensible naming convention, e.g. verb+noun for services/repositories, method+service name for controllers, noun+verb+past participant
