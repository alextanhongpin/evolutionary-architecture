# Repository

- Do we actually need repository?
- My initial thoughts is that we map a database table (e.g. mysql table `user`) to a `UserRepository`, ~~until I read that it is not the right way~~. This is not necessarily the case. We can either name the repository based on the `resource` name, or the `usecase` name. The latter is preferable. In other words, a repository can contain calls to different tables at a time. For example, a `product` repository can call the `user_address` table and also the `product` repository, or an `auth` repository can call the `token` table and also the `user` table to validate if the user exist and if the token has been used up. This makes code maintenance much easier, as we can simply delete the repository by the use case. For simple APIs such as plain CRUD, we can just use the _resource_ name as the repository name.


- the point of repository is not to make it possible to swap database (though it can be used for that, if the condition matches). Swapping a key-value store (e.g. leveldb) with a relational database (mysql) hardly works as the interface can differ a lot.
- repository makes it easier to mock the implementation by providing a interface for the data access, and hence makes testing easier. If you need to test a service that uses a repository, and say it's a mysql with multiple foreign key constraints (in order to query the product table, you need to first create a product, which requires a user to be created due to foreign key constraints which require the user address table to be first populated because the logic requires the address of the user etc), you can just mock the repository that returns the data without touching the database layer
- avoid placing business logic in the database. repository should be as dumb as possible. although some constraints can be placed there, but again we cannot assume that we are going to use the same database and both is able to perform the same query. business logic should best be placed in the service layer.


## Example in golang

```go
package main

import (
	"fmt"
)

// or User, if placed in repository/user.go
type Users interface {
	All()
	OneWithID(id string)
}

// Generic
type UserDAO interface {
	Create()
	Read()
	Update()
	Delete()
}

type UserRepository interface {
	GetOne()
	GetOneByID(id string)
}

// Place in userservice/repository.go.
// Preferred, use verb-noun convention.
type Repository interface {
	GetUsers()
	GetUserByID(id string)
}

type UserGetter interface{}
type UserSetter interface{}

// Use-case/service-driven repository.
// Resource-driven repository (normally plain CRUDs).
// Composite repository (for joins, or when having composite models).

// https://docs.microsoft.com/en-us/previous-versions/msp-n-p/ff649690(v=pandp.10)

// Typically the layers are: controller -> service -> repository
// Services should not call another service, nor can they call another repository. But there are exceptions.

// Split the service reponsibility by basic and custom. Basic are CRUDs. Customs can be search, approve (verbs).

// usermanager.go
type UserManager interface {
	// Avoid stutter.
	/*
		CreateUser()
		ReadUser()
		UpdateUser()
		DeleteUser()
	*/
	Create()
	Read()
	Update()
	Delete()
}

// Role is an attribute of the user, but we create another role manager here.
type UserRoleManager interface {
	Approve()
	Reject()
}

type UserAuthorizer interface {
	Authorize()
}

func main() {
	fmt.Println("Hello, playground")
}
```
