

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

## Aggregrates

- can be a combination of one or more entities
- is defined as a cluster of associated objects that we treat as a unit for the purpose of data changes
- performs actions between entities that are related to one another


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
