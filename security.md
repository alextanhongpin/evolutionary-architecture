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

