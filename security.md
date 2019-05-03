## Security

We can set the user and role for this to log the credentials of the user that accessess it.

```
$ echo -n app:user:role | md5
$ echo -n "simple text" | shasum -a 256
```


## Expiring JWT Token

Set a `version` in the `jwt` token, and set the equivalent `version` in the server that signs the token. If we ever need to kick all the users out of the server, then we just need to change the version.

## Should I tie a JWT token to a specific device (detect by user-agent)

No. https://stackoverflow.com/questions/38992239/json-web-token-make-it-client-specific

## Blacklisting JWT

It's not possible to implement actual logout with JWT. From the client side, we can simply remove the JWT token from the localstorage, but cannot prevent user from keeping a copy of it. To ensure that tokens that have been removed cannot be reused, we need to blacklist them.

One way is to store the blacklisted JWT token in Redis, and set the expiry in Redis by computing the time remaining for the token to expire. But this means for every calls that needs the authorization, they need to call Redis to check if the token is still valid or not.


## Custom Bearer

How to implement custom bearer

## Basic Authentication 

For ops login.

## Passwords in environment variable

A common mistake is to store plaintext user credentials in the environment variables. We need to distinguish the types of environment variables. It can usually be categorized as `config` or `secret`.

To pass in the credentials through environment variable, hash the `username:password` using SHA256 (or similar hashing that are strong enough). To compare, hash the input `username:password` credentials with those set in the environment variable.


## Encryption

What possible encryption methods are available...
