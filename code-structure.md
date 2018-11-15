## Bitwise Operator for conditionals

Use bitwise operator for handling large conditionals... TODO: Add example, when to use it, and when not to use it (over-engineering).

## Data injection pattern

This is inspired by the dependency injection pattern, which allows you to pass the dependency externally, hence making it easier to test your application. Take for example:

```
# Random pseudo-code, not language-specific
user = {
  username: 'john',
  token: token.new()
}
```

We have a user, and we want to generate the token for the user. One option is of course to make token as an interface, and pass the creation of the token from external. Another way is to simply mock the function creating it:

```js
model.injectToken(user)
validate(user)
```

## Create, then validate

This pattern can be used with mock dependency injection. Example, you have a token generator. It can be mocked to generate token, but here we are holding a huge assumption that it does the right thing. A better way is to perform validation after executing it:

```js
uuid = newuuid()
// Validate, can be through regex, or custom validation, checking for empty values, bad values etc. 
// Here we will query the cache/db to check if the ID exists in the db before putting it.
ok = db.has(uuid)
if !ok {
  // Handle error
}
```

## Step-wise application logic

Most of the time, we will have a large function that is composed of several dependencies, and we need to orchestrate the logic in a linearly-dependent way (requires the previous function to complete etc). But this make this function hard to test, as it is hard to test each steps independently. One option is to mock the dependency required for the steps - another is to just mock each steps. Note that we exclude db here, as it is already a mockable external dependency:

```js
// Some interesting observation - generic generators for random values.
user = step.generateUser()
time = step.generateFrozenTime()
token = step.generateToken(user, time)
db.user.save(user)

return token
```

In golang we can do it with functional optionals. But it means the code will get larger too - and it becomes slightly more complicated when you need to mock some of the data being returned.

## Model/Entity

An entity is a representation of an object in a domain. It can be a user object, for example. Say we have the following representation of a user:

```js
class User {
  constructor(name, age, id, updated_at) {
    this.name = name
    this.age = age
    this.id = id
    this.updated_at = updated_at
  }
}
```

The entity can have basic methods (setters/getters) that allows it to modify itself. It, however, does not have the capability to create it's own id, since the implementation is up to the business logic to decide (uuid, etc). That is why we will have another layer of `Model` on top of the entity. 

The same goes for `created_at`. Often, the logic that contains side-effects or mutations should not be placed at the `entity`, but rather the `model`. Take for example setting the `created_at` date.

```js
class User {
  constructor(name, age, id) {
    this.name = name
    this.age = age
    this.id = id
    this.updated_at = Date.now()
  }
  // Bad: This is not desired, as different domains might have different requirements for the isActive state of user. 
  // Say, if we have a Notification service, we might want to perform some conditional logic to determine if the user is active before sending them an email to encourage them to login. Or we might have another service to determine if the user is inactive for 1 year before sending them an email that their account will be deleted. In this sense, it's better to let the domain have control over the logic themselves, than the User Entity to have create a so-called `generic` function that caters for all logics.
  isActive() {
    // Perform logic to check if the updated_at is less than 2 hrs
  }
}
```

If we allow the user to initialize the date, it becomes harder to test the `isActive` function. Hence, the `model` should only contain setters/getters and the rights to assign the parameters should be given to the `model`. 

```
class UserModel {
  newUser() {
    // Returns a new user with the given parameter
  }
  isActive(user) {
    // Checks if the user is active
  }
  updateUser(user) {
    // Updates the user's updated_at date
    // Refer to the data injection pattern above
    // NOTE: On second thought, it should not modify the entity, rather it should return just the new values to the entity.
    // so rather than update user, it should be generateUser<FieldName>()
  }
}
// It can also be an anonymous function, since the domain can be dynamic, and you might end up with different variation of this factory pattern.
let newUser = function () {
  return new User()
}
```


## Combining different entity

Let's come back to the User and Notification example again. Say, we have this two supposedly independent entity, but we need both of them to create a new service - the Newsletter service, how do we do it?
```js
// Entities - holds the data.
class User {}
class Notification {}

// Model - contains business logic.
class UserModel {}
class Notification {}
```

Logically, we should have another facade/mediator/bridge in between this two services that will orchestrate the logic. It can be a simple function:

```
function sendDailyNewsletter (userModel, notificationModel, user, notification) {
  if (userModel.isActive(user)) {
      let preferences = notificationModel.checkPreference(user)
      let email = notificationModel.composeEmail(preferences)
      notificationModel.send(email)
  }
}
```

Note that in some cases, it might be overkill to create user models using classes. You can simply pass down an anonymous function.


## Functional patterns

Scoping functions with side-effects is essential for good code structure. Below we have a simple function that registers a user. It performs several other mutating operations internally:
```
function registerUser(user) {
  valid = validateUser(user)
  parsedUser = removeAdditionalFields(user)
  save(user)
  token = generateTokenWithExpiryTime()
  return token
}
```

One of the advantage of a huge function that encapsulates this side-effects is that it can be mocked (skipping all steps) and we can easily test all the functions:

```
function mockRegisterUser() {
  return 'fakeToken'
}
```

However, we are skipping too many steps. However, breaking them into smaller functions means more mock functions are required to test the functionality. So where is the boundary between splitting and placing it in a large function? This is a tough question, especially when we have multiple dependencies and we need to orchestrate them together. For a start, we can isolate the infrastructure-layer dependencies, such as database, logger etc. For utill/helpers, we can utilize the pattern above to reduce the side-effects. Especially for time-related fields, it's essential to isolate them. We can use an interface to define the operations, but will soon fall into the interface flood.

## Are Entity, Models and Repository the same?

Entity are just the representation of a domain object, and are stored into the repository. Models are responsible for transforming the entity/generating them into the correct format.

```
Entity (a.k.a types, model)
- standalone object that represents the data structure in the storage
- have basic getters/setters that is not tied to business logic
- may contain the basic validation for required/optional field, or types or min/max boundary, format (date, uri etc), but no domain-specific validation/business rules.
- has the ability to create a new basic entity (with no knowledge on the business rules), possibly initiating the values to the default values (empty array, default min age etc)
- e.g. We have a user entity, it can have getters/setters for name, age, email state. It can have basic validation for email, or name must be present, or age cannot be less than 0 etc. It has no knowledge on the business rules. They have to be applied to the entity.
- how is this different than model? An entity only represents the types of data. It should also provide a _basic_ initializer (not all languages support multiple constructors). If we need to customize the entity during construction, it should best be placed in the model, not entity. What are such cases? Let's take an example of creating a user with a role field. By default, it should only create a normal user. What if you need to initialize the the user as admin? Simple - if the role has to be defined all the time - pass in a flag when initializing. But if the assumptions is too vague - then create a factory to initialize it. There's no right or wrong in this case, and it's sometimes hard to find a sweet spot - because any way works. If you have a preferred method - stick with it.

Model
- may embed an object
- may embed a validation policy (specific to business rules)
- may create an Entity speficic to the domain. E.g. Create a new User entity with the role Admin assigned to it. 
- may perform data injection, removal, modifications, apply business logic to the entity as it deems fit. 
- may only perform operation on one entity (? how strict is this rule). The model should not be able to perform modification on several entity at the same time. That is the role of the service.
- may define interaction with the storage layer through repository patterns, but no strict implementation. Just through interface.
- ~~after a few attempts, I realized that the model should just be performing business logic. It should be pure functions - rather than modifying the entity, it should return the new values that needs to be set to the data. In other words, it acts as a pure provider, which is basically just a namespaced class with methods that returns values. This is especially useful when dealing with data that is hard to mock (token generation, time, random values). It should not modify the entity at all.~~
- update: do we even need a model? adding another layer only means more things to mock. 

Service (a.k.a usecases)
- may contain several models
- does not have direct access to the storage layer nor models - it should be communicated through the model.
- handles at most request/response at the highest layer. Mockable.
- may orchestrate complex business logic. 
- should be pure and contains no side-effects. Initialization of values should be the models responsibility, and creation of any models should also be done by models.
- could perform validation, but best for only nil/required values. Business logic validation should be left to the models.
```

## Some of the scenarios that needs to be considered

- what if a service needs to access multiple databases? simple - initialize the databases at the main function and pass them down through dependency injection. If they are not sharing logic - create a new service.
- where is a service needs to access multiple resources (same database, different collections/documents/table)? We can do a facade that orchestrates what needs to be shared - or look into patterns to decouple them like messaging/saga etc. Normally it is considered a violation to just allow a service to access multiple other resources. Another alternative is to create a new service that calls the different resources. Which means - another extra layer for that.
- where does the validation goes? Validations can and should be pure functions that returns an error after checking the fields. There are some exceptions such as checking if the user exists in the database, but we can compensate by using data structures such as bloom filter to avoid hitting the database. The only issue is you need to find a way to populate the bloom filter every time they restart - you can for example snapshot (?) the bloom filter on another server and sync it once the server restarts to avoid rebuilding the state.
- how to handle dependencies such as random number or time. This, unfortunately needs to be mocked externally, which means passing them down through dependency injection. One way is to provide a callback function, which if exists, allows you to modify the data that is supposed to be mutated. But by doing so, does testing makes sense anymore?

## No-op

In go (or any language with interface), it is better to create `Nop` structs that fulfils the interface, especially for dependencies that does not need to be tested (or just need to be marked as success test). This makes testing easier, since you do not want to test chaining the different logics at the same time. 

## Private methods in golang

You can keep fields in golang private. This is also another example of showing how to create a domain model with responsibility.
```go
type User struct {
  password string
  privateData interface()
}

// Ensure that whenever the password is set, it will always be hashed.
func (u *User) SetPassword(plaintext string) (err error) {
  u.password, err = hasher(plaintext)
  return 
}

// Allows only user with the valid password to access the private data.
func (u *User) AccessData(plaintext string) (data interface{}, err error) {
  // Use constant-time compare of course, this is just an example.
  if hasher(plaintext) != u.password {
    err = errors.New("unauthorized")
    return 
  }
  data = u.privateData
  return
}
```

## Better ways to validate?

There are several approaches to this:
- set, then validate object in group
- validate specific fields before setting
- validate when setting
- provide default if not exist

```js
// E.g., set, then validate. This is useful for grouping validation rules. If we know that the user object needs to have certain fields that needs to be validated, you can provide a facade of validation rules in a function. The downside is, if you need to handle an error differently, it can be a little harder. 
user.name = newName
validate(user)

// E.g. validate before setting. This is useful to decouple validation logic, and reuse them. 
// When you are validating just a specific field (e.g. regex for email), use this. If you need to handle specific errors differently, this will work the best.
if (!isValid(newName)) {
  // Handle error
}
user.name = newName

// E.g. validate when setting. This is very useful to enforce business logic. It is basically saying, if you don't provide a valid name, I will return an error.
const [modified, err] = user.setName(newName)

class User {
  constructor(name) {
    this.name = name
  }
  setName(name) {
    if (!name.trim().length) {
      // Throw error?
    }
    this.name = name
  }
}
```

To find the boundary of this, always bring it to the extreme. If you have a struct of 20 fields, with varying condition, you may even have to resort to all three different ways of validating the object, that is:
- validate required fields and default fields - or you can provide an object with those defaults and clone/destructure/apply new results on it
- handle specific errors 
- validate the whole object based on domain model etc

## Structs versus Methods arguments in golang

For a while I don't understand the true difference between structs and just plain functions. I've seen several code that does things differently - one is initializing the struct with dependencies and another is calling just the method with the interface passed.
```go
package user
type Service struct {
  repo Repository
}

func NewService(repo Repository) *Service {
  return &Service{repo}
}
func (s *Service) Method1 (id string) error {
  return repo.CheckExist(id)
}

// versus
func Method1 (repo Repository, id string) error {
  return repo.CheckExist(id)
}
```

What are the advantages/disadvantages of both of them. Let's take the former - which is initializing a struct. In this package user, we have a service struct. Let's see the different initialization method.
```go
package main
func main() {
  // I can call this, but the repo is not provided.
  usvc := user.Service{}
  
  // This is probably better, but gets more troublesome when the dependencies grow.
  usvc := user.NewService(repo)
  usvc.Method1("1")
  usvc.Method2("1")
  usvc.Method1000()
  // We are experiencing namespacing here. We create a namespace for the set of methods we want to use from the service. This is useful if all the services are related to one another. If you only need to call one method, e.g. `Method5` from the user package, this is slightly overkill. Why do I have to initialize the whole struct just to call one method?
  
  // Compared to this. I am calling `Method1` directly (independently without needing to initialize the service namespace). I can also (can be repetitive) pass in a different type of repos when calling `Method1`.
  user.Method1(repo1, "1")
  user.Method1(repo2, "1")
  // Looks similar to the standard go convention, fmt.Fprintf(f, "data"), os.Write(f, "data")
  // Would be troublesome when you have multiple dependencies...But hey, one method should only do one thing right? Passing down the unused dependencies to the namespaced struct is even worse.
  user.Method1(repo1, rand1, anotherclass2, "data")
  
  // How about hybrid?
  // Defined a userservice with a repo
  usvc := user.NewService(repo)
  
  // Calls external dependency with through methods instead
  usvc.Method50(dependency, "1")
  
  // Or, create a package user that provides just pure functions, then create another internal package that groups similar functions and initializes all the dependencies. This is probably the most preferred way. 
  // so, package `user` would have the functions
  user.Method1(repo, "1") // No more user.NewService()! Can be tested independently!
  // and another package usersvc would have a group of methods
  svc := usersvc.New(repo) // No more stutter!
  svc.Method1("1")
  
  // Bringing it to the extreme - 10 deps, 100 methods
}
```

## Mocking non-deterministic data

There are some methods that will provide random values. This is usually not desirable in tests, because every call will return a different value. E.g. crypto, random number generation, time, id, database(!) etc.

One way is to simply create a mock for each of them. But this gets troublesome - do we have to mock the entire thing just to return that desired value, say the whole time package? No, we can just create another interface to get the specific method that produces that value. Or, we create `Provider` (or `Generator`, `Requester`, if you prefer another term), a one time method that returns the value. The term provider does not suggest a creation of classes with method, rather it can be plain anonymous function that is passed in the function.

```go
// Option 1: Pass as interface
func CreateUser(provider Provider){
  u := user.New()
  u.ID = provider.NewID()
  // ...
}

// Option 2: Pass as first-class function. We choose a function, but you can also replace it with a value directly, for e.g. the newly created id. That way
func CreateUser(fn func () string) {
  u := user.New()
  u.ID = provider.NewID()
  // ...
}

// Option 3: Structs with initialized dependencies
type User struct {
  provider Provider
  // a
  // b
  // c
  // d
  // e
  // If we need to mock 10 fields in one method, we need to initialize here 10 times. If they are unused by other methods, it looks undesirable.
}

func (u *User) CreateUser() {
  u := user.New()
  u.ID = u.provider.NewID()
  // ...
}
```

The problem with this is where to pass down the `Provider`? If we perform Dependency Injection, it makes sense to pass it down during the initialization of the class. But again, passing down one dependency to be used by just one method doesn't seem desirable. 

It's a tough problem. First, we have to ask ourselves if we can live without knowing the exact value generated? If it's an id generation service, we definitely know that it would provide random values, so if there's little value in testing it, then we can skip the trouble. But if it has something to do with time, and we want to test expiration date, then it becomes slightly more troublesome.

We can follow the pattern in golang's `rand` package, by creating a global seed. But this would work well only if you don't modify the values often. When testing expiry time, we need to initialize it with our desired time, and test the possibilities. This means we need to initialize the values often. But for that, we probably do not need to test the generation of the random values, but rather the business logic (what is considered an expired time, what are the conditions and boundaries. If my function accepts a value, what can be deduced from it?).


## Polymorphism
I had to deal with structs that looks like this:
```go
type User struct {
  Profile *Profile
  Address *Address
  Email *Email
  Scope int
}
```

Based on the scope, it should choose to set the values for Profile, Address and/or Email. There are several problems here. 1) It does not map well to the data storage layer. It has nil pointers, which makes initialization clunky, and also potential of panic. I need to perform safety check to ensure I'm not hitting nil. 

A better way to refactor this is to remove the nil pointers and just flatten the data. Then use bits to indicate the scope (or linux ownership rwx). 

## Always bring it to the extreme

Some cases are too small to detect useful patterns. 

## Model in golang

I've been using the models to call the repository in golang for quite a while, then I realise it's an anti-pattern. Because it kept things nested (model needs to initialize the repository) and there can be a lot of unnecessary layer when it's just a plain CRUD (service calls model, which just call the repository). It also makes testing harder, due the initialization sequence (repo -> model -> service). A better way is to just let the model take the parameter it needs, and validate it. So model and repository stays in the same layer. This makes testing a whole lot easier, since we do not need to initialize the repository to mock the results before calling the model. Instead, the values can be passed to the model directly. 

## If/else business logic vs Chain of Responsibility

Rather than having an if/else statement, we can instead use chain of responsibility to delegate the work to be done. This is particularly useful when testing, because we can scope the work and test the logic independantly on the hardcoded if/else in the code section.

## CQRS Naming Convention
It's hard to find proper CQRS naming convention, hence I've decided to find what's useful and add it here. Some useful guideline is:
- separate read and write
- separate command and events
- use `verb-noun` for commands
- use `noun-verb` for events in past tense

E.g. Write Service

| Command | Event |
| --------|-------|
| CreateEvent | EventCreated |
| ChangeCustomerLocale | CustomerLocaleChanged |
| MakeCustomerPreferred | CustomerPreferredMade |
| EditCustomerDetails | CustomerDetailsEdited | 

Note that it is best to avoid `REST` terms like `UPDATE`, because there's a loss of information here (what is being updated?). In other words, be as specific as possible and group necessary fields that needs to be updated. E.g. `UpdateUserPreference`, `UpdateUserAddress`. If possible, all `WRITE` services should be logged, since they change the state of the application, unlike `READ` services.

E.g. Read Service

| Command | Event |
| --------|-------|
| GetCustomer | - |
| GetCustomerByName | - |
| GetPreferredCustomer | - |

For `boolean/flags`, you can use the term `Mark/Set` to indicate status change. Don't use ambiguous term such as `Toggle` - they do not provide information of the end state of the action. E.g. when delivering a food - `MarkFoodDelivered`, or order `MarkOrderConfirmed`.

For `update`, note that it can give more meaningful context compared to traditional CRUD. Say we have a separate Ops team to handle user orders, and the user called the customer service to amend the changes made to the order (which is not possible through the webui), we can create an event called `AmendCustomerOrder`, rather than the `UpdateCustomerOrder`, which can serve as a meaningful metrics to find out how many customers made mistakes or other useful data.
