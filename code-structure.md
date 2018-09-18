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

```
model.injectToken(user)
validate(user)
```

## Create, then validate

This pattern can be used with mock dependency injection. Example, you have a token generator. It can be mocked to generate token, but here we are holding a huge assumption that it does the right thing. A better way is to perform validation after executing it:

```
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

```
user = step.generateUser()
time = step.generateFrozenTime()
token = step.generateToken(user, time)
db.user.save(user)

return token
```

## Model/Entity

An entity is a representation of an object in a domain. It can be a user object, for example. Say we have the following representation of a user:

```
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

```
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
Entity 
- standalone object that represents the data structure in the storage
- have basic getters/setters
- may contain the basic validation for required/optional field, or types or min/max boundary, format (date, uri etc), but no domain-specific validation/business rules.
- has the ability to create a new basic entity (with no knowledge on the business rules), possibly initiating the values to the default values (empty array, default min age etc)
- e.g. We have a user entity, it can have getters/setters for name, age, email state. It can have basic validation for email, or name must be present, or age cannot be less than 0 etc. It has no knowledge on the business rules. They have to be applied to the entity.

Model
- may embed an object
- may embed a validation policy (specific to business rules)
- may create an Entity speficic to the domain. E.g. Create a new User entity with the role Admin assigned to it. 
- may perform data injection, removal, modifications, apply business logic to the entity as it deems fit. 
- may only perform operation on one entity (? how strict is this rule). The model should not be able to perform modification on several entity at the same time. That is the role of the service.
- may define interaction with the storage layer through repository patterns, but no strict implementation. Just through interface. 

Service
- may contain several models
- does not have direct access to the storage layer nor models - it should be communicated through the model.
- handles at most request/response at the highest layer. Mockable.
- may orchestrate complex business logic. 
- should be pure and contains no side-effects. Initialization of values should be the models responsibility, and creation of any models should also be done by models.
- could perform validation, but best for only nil/required values. Business logic validation should be left to the models.

```
