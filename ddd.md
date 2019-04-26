

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

https://airbrake.io/blog/software-design/domain-driven-design
