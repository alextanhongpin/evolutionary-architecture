## A basic use case

```
UseCase: Gather info for new loan
Input: Name, address, birthdate, etc
Output: Same info + credit score

Rules:
1. Validate name
2. validate address
3. get credit score
4. if credit score < 500: activate denial
5. else create customer (entity) and activate loan estimation
```

References: https://pusher.com/tutorials/clean-architecture-introduction

## Example Use Case

| Topic | Description |
| - | - |
| Title | Register for courses |
| Description | Student accesses the system and views the courses currently available for him to register. Then he selects the courses and registers for them. |
| Primary Actor | Student |
| Preconditions | - Student is logged into system <br/> - Student has not previously enrolled or registered <br/> - Student cannot register within 5 days of course start date |
| Postconditions | Student is registered for courses | 
| Main Success Scenario | 1. Student selects "Register New Courses" from the menu. <br/> 2. System display list of courses available for registering. <br/> 3. Student selects one or more courses he wants to register for. <br/> 4. Student clicks "Submit" button. <br/> 5. System registers student for the selected courses and displays a confirmation message. | 
| Extensions | - (2a) No courses are available for this student <br/> 1. System displays error message saying no courses are available, and provides the reason and how to rectify if possible. <br/> 2. Student either backs out of this use case, or tries again after rectifying the cause. <br/> - (5a) Some courses could not be registered. <br/> 1. System displays message showing which courses were registered, and which courses were not registered along with a reason for each failure. <br/> - (5b) None of the courses could be registered. <br/> 1. System displays message saying none of the courses could be registered, along with a reason for each failure. |


## Template
```
Title: <perform action>
Description: <actor> accesses the system and wants to <perform action>
Primary Actor: <actor>
Secondary Actor: system
Preconditions:
- <actor> is logged into system
Postconditions:
- <actor> successfully completed <action>
Main success scenario:
- 1. The use case begins when <actor> <perform action>
- 2. <actor> <perform another action>
- 3. <system> respond with <response>
Extensions
- (1a) when <null condition>. 
    - <system> should respond with <message>.
    - <actor> backs out of this use case.
```

## Example Passwordless Login
```
Title: Passwordless login
Description: User ask system for a passwordless login token
Primary actor: User
Preconditions:
- User have a valid e-mail address
- User is not logged in
Postconditions:
- User is logged in the system
Main success scenario:
- 1. The use case begins when User is at the login page
- 2. The User enters the email address
     Business Rule: email must be valid
- 3. The User click submits
- 4. The system validate the email
- 5. The system sends an email containing a token that is valid for 5 minutes
- 6. The User clicks the link on the email
- 7. The system checks if the token is valid
     Business Rule: token has not expired
- 8. The system logs the user in
Extensions:
- 4a) The email is not valid
    - The system display an error message and ask the User to submit a new email
    - The User can opt to back out of this use case
- 5a) The User did not receive the email
    - The User back out of the use case and start from beginning
- 7b) The User clicks the link after token is expired.
    - The system display an error message saying that the token is expired
    - The User back out of this use case and start from the beginning
```   
# References

Use Cases:
- https://fullstackmark.com/post/11/better-software-design-with-clean-architecture
- http://www.eg.bucknell.edu/~cs475/F04-S05/useCases.pdf
- https://en.m.wikipedia.org/wiki/Use_case
- https://creately.com/blog/diagrams/use-case-diagram-guidelines/
- http://tynerblain.com/blog/2007/01/22/how-to-write-good-use-case-names/
- http://tynerblain.com/blog/2007/04/09/sample-use-case-example/
- http://www.agilemodeling.com/artifacts/systemUseCase.htm#Figure1
- ftp://ftp.software.ibm.com/software/rational/web/whitepapers/RAW14023-USEN-00.pdf
- https://www.ibm.com/developerworks/rational/library/content/legacy/parttwo/1000/0669/0669_Rosenberg_Ch03.pdf
- https://www.ibm.com/developerworks/rational/library/content/RationalEdge/may02/m_chapter4_jr.pdf
- https://www.ibm.com/developerworks/rational/library/content/legacy/parttwo/1000/0670/0670_Schneider_Ch07.pdf
- https://www.ibm.com/support/knowledgecenter/en/SSWSR9_11.0.0/com.ibm.pim.dev.doc/pim_ref_usecasetemp.html

UML 2.0
- http://agilemodeling.com/style/useCaseDiagram.htm

## Naming

Manager, Director, Supervisor, Controller, Administrator, ...

- order_manager: can view, create, update and delete orders
- order_editor: can view, create and update orders, but not delete them
- order_inspector: can only view orders

## References

- https://stackoverflow.com/questions/1866794/naming-classes-how-to-avoid-calling-everything-a-whatevermanager

## Scope and roles

- Scope: the resource you want to access. 
- Roles: the actor responsibility

Determining `scope` and `role` from user story:

```
As an Admin (role),
I want to View Products (scope)

As a User (role),
I want to View Products (scope)

As an Admin (role),
I want to Delete Products (scope)
```

Table:

| Scope | Role |
| - | - |
| View Products | Admin, User |
| Delete Products | Admin |

In the service:

```
router.get('/v1/products', scope(Admin, User), controller.getProducts)
router.delete('/v1/products/:productId', scope(Admin), controller.deleteProducts)
```

- Sample roles includes: Admin, User, Developer, Employee
- Sample scopes are named in the pattern `<action>:<resource>`: `create:user`, `update:user`, `delete:user`, `register:events`, `read:image`. For standardization purpose, use singular for resource.

References:
- https://wso2.com/library/articles/2015/12/article-role-based-access-control-for-apis-exposed-via-wso2-api-manager-using-oauth-2.0-scopes/
- https://auth0.com/docs/scopes/current
- https://auth0.com/docs/scopes/current/oidc-scopes
- https://auth0.com/docs/scopes/current/api-scopes
- https://auth0.com/docs/scopes/current/sample-use-cases#request-custom-API-access
- https://auth0.com/docs/scopes/current/custom-claims
- https://auth0.com/blog/on-the-nature-of-oauth2-scopes/
- https://www.keycloak.org/docs/latest/server_admin/index.html#roles
- https://www2.cs.duke.edu/courses/spring04/cps108/readings/usecaseslarman.pdf
- https://sites.cs.ucsb.edu/~mikec/cs48/project/UseCaseRequirements.pdf
- https://courses.cs.washington.edu/courses/cse403/04wi/materials/DrDan/UseCaseChapter.pdf

## User stories and use cases are different

## Documenting State machines

Loop:

| Prev state | Next state |
| - | - |
| Green Light | Orange Light |
| Orange Light | Red Light |
| Red Light | Green Light |

Finite:

| Prev state | Next state | Action |
| - | - | - | 
| - (start) | Pending Approval | Payment Made |
| Pending Approval | Paid | Payment Approved | 
| Pending Approval | Rejected | Payment Rejected | 
| Rejected | Pending Approval | Payment Retry | 
| Paid | - (end) | - |


## Folder structure

```


service/usecases
- user/(package user)
	- usecase.go (user.UseCase/user.Service)
	- model.go (user.Model)
	- controller.go (user.Controller)
	- repository.go (user.Repository)
- book/
	- usecase.go
	- model.go
	- controller.go
	- repository.go
- userbook/
	- usecase.go
	- model.go
	- controller.go
	- repository.go
- auth/
	- usecase.go
	- model.go
	- controller.go
	- repository.go

// Holds interfaces. But this seems redundant, keep the interface close to the caller that implements them.
- repository/ (package repository)
	- user.go (repository.User)
	- book.go
	- userbook.go
- model/
	- user.go
	- book.go
	- userbook.go
- service/
	- user.go
	- book.go
	- userbook.go
	
// This works, assuming there's only one role - which is the user accessing the resources. 
// What if we have an Ops role that can manage book/reviews?
// In this case, we have two main roles - the Owner of the resource, and also the Ops.
// The Owner can view the data differently, such as they can only manage their data (delete etc), and only have Read-only access for other user's data.
// book_manager (for admin, CRUD)
// book_editor (for ownself, CRU, no Delete)
// book_inspector (for others, R only)

There are many constraints, such as user can only manage their own books (which means we need to know if the owner owns the book, which requires an additional query).
It can be pain to define all Scopes for different Roles, we can simplify it by saying all Ops are Manager, and authenticated Users are Editor, and if the resources belong to them, they are Owner.

Some scenarios:
- User A owns Book A.
- User A can update Book A.
- User B cannot update Book A.
- User A, B and Ops can view Book A.
- Ops can update Book A's approval status.
- Ops can delete Book A.
- User A and B cannot delete Book A.

// Where should the conditional check be? in the Controller, or Service? 
// Or a RoleManager that checks the roles and delegates the responsibility?
// Nevertheless, there will be a function that handles the book deletion by roles:
// func DeleteBooks () {...}
switch role {
	case Ops:
		// Owner delete books by soft-delete, and change the status to approved.
		usecase.DeleteBooks()
	case Owner:
		// Owner sends request to delete book.
		usecase.RequestDeleteBooks()
	default:
		// Unable to process...
} 
// Having conditionals for the same endpoint is nevertheless a bad idea.
// Another option is to create separate endpoints for Ops and Users. 
// This makes the services independent from one another, and it is easier to decouple the implementation.
// There will be code repetition - but again we can create a "common" repository for code reused.
// But for other cases, code reusability will again couple implementations, best to avoid and keep things simple.


// If only one Role can access the Service, use a Middleware to limit the access:
router.DELETE("/books", roleChecker.Only(Admin), controller.DeleteBooks)
```


## Folder structure

```go
package main

import (
	"context"
	"fmt"
)
// Per role grouping. Four main files, usecase.go, repository.go, controller.go, and model.go.
// This will be in usecase.go, and repository.go will have Repository interface.
type UseCase interface {
	// Reader.
	// We might be tempted to write this, but use this for Repository.
	// When defining a Service/UseCase, use the equivalent UseCaseRequest/UseCaseResponse struct.
	// This is because we can easily use libraries that validates the struct, rather than individual arguments.
	// GetUser(id string) (*User, error)
	GetUser(context.Context, GetUserRequest) (*GetUserResponse, error)
	GetUsers(context.Context, GetUsersRequest) (*GetUsersResponse, error)
	GetUserByEmail(GetUserByEmailRequest) (*GetUserByEmailResponse, error)

	// Writer.
	CreateUser(context.Context, CreateUserRequest) (*CreteUserResponse, error)
	UpdateUser(context.Context, UpdateUserRequest) (*UpdateUserResponse, error)
	DeleteUser(context.Context, DeleteUserRequest) (*DeleteUserResponse, error)
	UpdateUserRole(context.Context, UpdateUserRoleRequest) (*UpdateUserRoleResponse, error)

	// Custom.
	SearchUser(context.Context, SearchUserRequest) (*SearchUserResponse, error)
	Login(context.Context, LoginRequest) (*LoginResponse, error)
	Register(context.Context, RegisterRequest) (*RegisterResponse, error)
}


// Per file grouping. Many files which contains a usecase, repository each. One file containing model and controller respectively.
// Define each use case in a file with repository.
type LoginUseCase func(LoginRequest) (*LoginResponse, error)

// This use case is driven by repository logic.
type LoginUseCaseRepository interface {
	CreateUser(email, password string) error
	CheckUserExist(email string) error
}

func NewLoginUseCase(repo LoginUseCaseRepository) LoginUseCase {
	return func(req LoginRequest) (*LoginResponse, error) {
		err := repo.CheckUserExist(req.Email)
		if err != nil {
			return err
		}
		passwd, err := hashPassword(req.Password)
		if err != nil {
			return err
		}
		return repo.CreateUser(req.Email, passwd)
	}
}

// UseCase ith multiple methods...
type CreateOrderUseCase interface {
	CheckOrderExist()
	// use other services...
}

func main() {
	fmt.Println("Hello, playground")
}

```

## Generating TestCases from UseCase

If we have the use case with three steps, and each of them has an extension:

1. step a
2. step b
3. step c

extensions:
1a. extension_a
2a. extension_b
3a. extension_c

Then we will have the following test cases combination:
- step a, step b, step c (all success)
- none (fail BEFORE step a)
- step a, extension_a
- step a, step b, extension b
- step a, step b, step c, extension c

https://www.computing.dcu.ie/~ltuohey/CA314_2004_2005/GeneratingTestCasesFromUseCasesJune01.pdf

# Create domain model

User is an actor, User Account is a domain model. Start of by recognizing the domain models in your application, and build use cases around them. Model the behaviours around the domain models too.
