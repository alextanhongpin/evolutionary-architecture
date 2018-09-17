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
