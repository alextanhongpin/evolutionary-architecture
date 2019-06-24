# Access Control Lists

There are different approaches on setting up ACLs (Access Control Lists, but not to be confused with [Unix Permissions](https://en.wikipedia.org/wiki/File_system_permissions)) for microservices. Indeed, there are many other overlapping terminologies such as roles and scopes too. OpenID defined several basic [scopes](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims), such as `openid`, `profile`, `email`, `address` and `phone`. More on scopes [here](https://github.com/alextanhongpin/evolutionary-architecture/blob/master/role_and_scopes.md).

## Attribute Based Access Control (ABAC)

ABAC may seem to overlap with RBAC, but that comes naturally, simply because ABAC can implement RBAC. A `role` can be another attribute for ABAC, and it is easier to extend ABAC than RBAC too.

https://blog.identityautomation.com/rbac-vs-abac-access-control-models-iam-explained

## Role-Based Access Control (RBAC)

http://jasonwatmore.com/post/2018/11/28/nodejs-role-based-authorization-tutorial-with-example-api
https://developer.okta.com/books/api-security/authz/role-based/#role-based-access-control-authz-role-based

## IAM

https://cloud.google.com/appengine/docs/admin-api/access-control
https://wso2.com/whitepapers/identity-architect-ground-rules-ten-iam-design-principles/

See also:
- https://github.com/alextanhongpin/database-design/blob/master/role.md

## JWT Claims

Roles and/or scopes are usually returned together with the JWT Token. This may pose some issue, because:

- changing roles means the existing token needs to be blacklisted, and the client needs to fetch a new jwt with the new role. If the roles change frequently, this could pose a problem.
- if the token duration is long and the role is removed, the user might have undesired access to the application.

## APIs behaviour for different Roles

How can we design our APIs for it to implement different behaviours based on different roles? 

- some suitable design patterns could be `chain of responsibility`, `strategy`, or `abstract factory`.
- switch statement/branching condition. But this is probably not the best solution.
- separate the API endpoints for different roles by prefixing the roles and enforce the checking for different endpoints. E.g. for own content, use a root-level `me/` endpoint, for admins/operators access, use `/ops`. 
- similar as above, but separate through subdomain name, e.g. `ops.api/v1/users`
- setup middlewares to check the roles


## ACL Admin

ACL that allows you to limit user's access to certain operation (read, write, etc) and allow you to invite new users through email. Basic implementation with nodejs [here](https://github.com/alextanhongpin/node-scope).


## Ops Access

Allow privilege access to APIs if the user is an Ops (Operator/Admin) etc. Tokens can be generated for the user, which is audited to ensure that the ops are not performing any malicious activity. Last activity, last login date can be tracked etc.
