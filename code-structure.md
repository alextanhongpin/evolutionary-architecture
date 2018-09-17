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
