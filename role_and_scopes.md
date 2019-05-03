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

References:

- https://github.com/alextanhongpin/database-design/blob/master/role.md
