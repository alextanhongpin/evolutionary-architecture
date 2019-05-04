## Role and Scopes

For each resource, set the scopes for each action. A typical CRUD API for a book will look like this:

- book:create
- book:read
- book:update
- book:delete

Custom actions can be a separate scope:

- book:search
- book:approve

Now that we have the necessary scopes, let's define the roles. Assuming we have a user that could add, update and delete books (book author) and one that can approve (admin), then we could have the following roles:

- author
- admin
- user (user can only view the books)

For each roles, we assign the scopes to them:

- author: book:create, book:update, book:delete
- admin: book:approve
- user: book:read

We will face a lot of repetition with the create, update and delete scope. To simplify it, we can place them under a parent scope, `book:manage`. The updated roles and their scopes now looks like this:

- author: book:manage
- admin: book:approve
- user: book:read

## Finer grain permission

It is more scalable to detect the scopes by the user roles, since the scopes are normally resource-specific (`book:create`, `book:update` etc), and adding more APIs means more scopes, and the user needs to be given a new token with the scopes. But if we just return the user's role in the token, then we can easily extend the scopes on the server side, hence adding in new scopes will be more seamless.

For finer grained permissions, such as allowing only certain fields to be queried/updated, we can add the fields for each scope. E.g. `update:book` only allows the `title` to be updated.

## Me and yours

For the access controls, we made a simple assumption that for any actions that can modify a resource (e.g. update), only the user's can update their own (`my`) resources, which is the one they created. It should not be possible for them to update other resources that do not belong to them, unless if they hold the `admin`, or `ops` role. 

But how about read scopes? What if we are going to implement a blocking action, and we can prevent a user from reading your resource (note, for this to take affect, the endpoint must first be protected, which is to say only authorized user can view the resource. Else, if the endpoint is public, there's no way to tell if the user is who they say they are). 

There can be several level of blacklist, field level, user level or resource level. For resource level, only the blacklisted party cannot access certain resource. Field level brings it to the next level, the blacklisted party cannot view certain fields only (shadow banning!). User level is the simplest (?) in theory, the blacklisted party cannot see anything created by the user. 

This is slightly more complicated in practice, since we need to know each user's block list and validate that at content retrieval.

## Types of ACL

- unix ACL
- RBAC role based access control
- ABAC attribute based access control (preferred)

References:

- https://github.com/alextanhongpin/database-design/blob/master/role.md
- https://developer.okta.com/books/api-security/authz/attribute-based/#authz-attribute-based
