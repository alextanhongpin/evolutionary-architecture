# Authentication for Single Page Application (SPA)

- ability to revoke token is important
- showing list of sessions is also important
- storing access/refresh token locally in localStorage/sessionStorage is insecure
- appending 'Authorization ' + token on client side is insecure, this should be done through a reverse proxy with session enabled, and appended there before calling the target resource server
- otherwise, the token could also be stored in-memory, but will be lost upon refresh/tab change. The client must then call the proxy server to obtain the token through a valid refresh token
- refresh token is one-time use only, after using it, it will be revoked and a new refresh token is issued (each could be extended with a new expiration date, so as long as the user is active, their token will be long living on the server side)
- you still need a backend server for persisting session

# References

- https://medium.com/@benjamin.botto/secure-access-token-storage-with-single-page-applications-part-2-921fce24e1b5
- https://auth0.com/docs/security/data-security/token-storage
- https://wso2.com/blogs/thesource/securing-spas-best-practices/
- https://auth0.com/docs/security/data-security/token-storage
- https://flaviocopes.com/graphql-auth-apollo-jwt-cookies/
