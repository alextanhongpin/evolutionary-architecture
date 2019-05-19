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

## Replay attack

- timestamp event/requests
- Events without timestamp is invalid
- If the event is out of time(less than 5 minutes/validity period ago, or from the future), it is invalid
- If the action to be taken is only valid for a certain period, set a due date. Action must be carried out within that period
- Set a date on the entity to be updated, for the next change, check if the ts is greater than the previous timestamp, if yes, only update it

## Performance and security as a feature

## Business Login in JWT

JWT should not contain business logic - if the token holds claims that are dynamic, any change to the data requires the old token to be invalidated/blacklisted, and the client has to fetch a new token with the new claims.

JWT Token is best used for `authentication` (who the caller is), even when it can perform `authorization` (what the caller can do) through claims.

## Setting Roles in JWT

JWT token is like a passport - it represents the identity of the user at the time the token is generated, until it expired. Thus, we carry along an assumption that the claims set in the JWT are static and should not change frequently. Roles however can be dynamic. Changing the roles on the server side will still leave the existing JWT token with the old roles.

This can have some serious security consideration, imagine giving an admin access to the token. Even when the token is short-lived, the amount of harm that can be done is immeasuarable. Some possible solution (and problems they introduce):

- maintaining a separate blacklist table, which now makes the application stateful. Also, the blacklist table needs to be stored in a distributed cache to avoid load-balancing issue or role-checking on different application for the same user.
- check the db everytime a requests is send to validate the role (either an independent call, or in the statement query).


## Fuzzy testing (?) with strings

https://github.com/minimaxir/big-list-of-naughty-strings

## Anomaly Detection for requests

In order to accomplish this, we need to detect the usage pattern for each users on the different endpoints. Endpoints such as login will normally be rarely called, hence having a increase request over a short time frame can be suspicious.

- how to detect low requests (below the rate limit) abuse?
- how to detect scraping (if they call it at fixed interval or non-fixed interval)
- how to perform time-series anomaly detection?

## Fraud detection

This may be different than anomaly detection, that it detects the attempts of creating fake resources (accounts, orders) and testing the waters.

## Abuse and DDOS

Abuse and DDOS can be prevented by adding rate-limit to the system, and blacklisting the IP address that exceeded the rate limit frequently. There are active and passive systems that detects the abuse.

- active. State is hold in application. The ip is immediately added to blacklist once the threshold is exceeded, and the user is banned.
- passive. Through log analysis. The request logs are sent to a system to be analysed, and the response is then feed back to a distributed storage (redis etc) and refreshed by the application (or load balancers), which then blacklists the ip.

## References
- https://stackoverflow.com/questions/47224931/is-setting-roles-in-jwt-a-best-practice
- https://assertible.com/blog/api-security-testing-tips-to-prevent-getting-pwned#test-for-authentication-on-all-endpoints
- https://github.com/shieldfy/API-Security-Checklist
