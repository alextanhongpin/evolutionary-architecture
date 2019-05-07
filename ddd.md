

## Entity

Entity has 
- an identity (id, can be unique, can be primary, foreign keys or combination of keys)
- has behaviour (that modifies itself)
- has attributes (that defines itself)

## Value Objects

- compared with values only
- does not have an identity (e.g. Money(5) is the same as Money(5) even if they are two different objects)
- usually used to define types that are 1 level above primitives (int, string etc). E.g. use Age instead of primitive integer, because Age cannot be negative, have a boundary value, etc


## Service

- is stateless
- can perform operation on an entity 
- use to orchestrate interaction between different entities (e.g. order item), like a saga orchestrator (?)
- if the behaviour does not belong to an entity, it will normally be implemented here
- a.k.a usecases
- may sound like aggregate, but it is actually different. Aggregate root manages a bunch of entity and ensure the state is valid/consistent.

## Aggregrates

- can be a combination of one or more entities
- is defined as a cluster of associated objects that we treat as a unit for the purpose of data changes
- performs actions between entities that are related to one another

https://medium.com/@unmeshvjoshi/aggregate-oriented-microservices-d314eb04f2b1
https://softwareengineering.stackexchange.com/questions/237943/domain-services-vs-factories-vs-aggregate-roots

```go
package main

import "fmt"

type Person struct {
	Name string
	Age  int
}

type PeopleManager struct {
	people []Person
}

func (p *PeopleManager) Adults() []Person {
	var result []Person
	for _, i := range p.people {
		if i.Age > 10 {
			result = append(result, i)
		}
	}
	return result
}

func main() {
	people := []Person{
		{"john", 10},
		{"jane", 20},
	}
	pm := PeopleManager{people}
	adults := pm.Adults()
	fmt.Println(adults)
}
```


### Things to consider when designing a model

- the attributes
- the identity
- the behaviour
- the association
	- single entity. can it be one, can it possibly be two, can it be a fixed number, can it be unlimited?
	- multiple entity (e.g. a car and engine can have only one car_engine). can it be one, can it be ...
- uniqueness
- aggregates (what entity is dependent on another)
- saga + state machine (what interaction/steps/process flow that is required in order to reach the final state)
- is it an immutable object
- does it have a status? do we need to keep track of the status
- does it have a sequence of state that can change over time? can it be a time series?
- does it have a validity range (valid from, valid till)? can it expire?
- when it expires, does it still play a role (historical/readonly, but no actions). E.g. pricing information
- does it need versioning? version 2 wil have a different business rules from version 1. version 1 is deprecated, version 3 is created, and version 3 can coexists with version 2.
- which is the aggregate root? when deleting the root entity, how does it affect the rest? will they be deleted?
- are the entity immediately related? or can they be delayed? (e.g. order can be created, but the payment can be later. once the payment is completed, it needs to be verified, but it will also depends on the admin that manually updates it).
- let's break it down, it can have diferrent statuses, relationships, lifecycle, version

https://airbrake.io/blog/software-design/domain-driven-design

## Other userful terms

We can probably break down our services into the following:

- Service orchestration is the coordination or integration of several services to expose it as a single service.
- Service aggregation act as a facade over the lower level services, hiding technical details and interrelationship from the service consumer. Service aggregation adds alot of value to the overall system by providing a higher level of abstraction. Tool: Oracle Service Bus (OSB-Proxy Services), for example.
- Service Augmentantion is used within a service for message enrichment. Suppose that based on client id you need to obtain additional data, like address, email or phone, you should augment message using database queries, etc..

It seems like most of the services (CRUD) written can be classified as service augmentantion. For most CRUD, the service layer tends to be redundant, since it is only a layer that makes a call to the repository. This is especially more apparent for services that are mainly GET (getters).

## Code Structure

Infrastructure layer
- Container
- Factories
- Persistence 
- Configuration

Presentation layer
- Api
- middlewares

Domain layer
- E.g. booking
- Delivery 
- Order
- Usecases


