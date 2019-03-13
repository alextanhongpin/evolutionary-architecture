## Middleware

- Middleware are recurring capabilities business rules. E.g. Authorizing user, user roles and scopes.
- They can be think as a decorator too
- Middleware naming should hint at their capabilities, it should be a noun with -er suffix. Sometimes it can be a bussiness name too (e.g. CORS, JWT)

```
// Bad
JWTToken, Token, Authorize

// Good
middleware.BearerAuthorizer
middleware.BasicAuthorizer
middleware.RoleChecker
middleware.RequestIDProvider
middleware.Logger

// Business name.
middleware.JWT
middleware.CORS
```

- avoid putting too many business rules in the middleware - it makes testing harder
- middleware may contain preconditions from _use cases_
