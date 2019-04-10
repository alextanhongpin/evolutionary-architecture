It is best to log any errors/exceptions that are happening to the client side. That way, we can detect unexpected errors that occurs in production that are not detected in development.

Some of the common errors could be as simple as:

- wrong json payload
- accessing a nested object that doesn't exist
- null pointer
- app crash
- wrong api endpoint

~~If the clients are product specific and they are calling multiple APIs (services), then it is best to send the errors to their respective service.~~

Just send the errors to a centralized logging. But ensure you attached enough metadata such as the application that is calling, user agent, device, which api calls are registered etc, to make it easier to track the changes.

References:

- https://developers.google.com/web/updates/2018/09/reportingapi
- https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API
- https://blog.sentry.io/2016/01/04/client-javascript-reporting-window-onerror
- https://logmatic.io/blog/javascript-errors-and-logs/
