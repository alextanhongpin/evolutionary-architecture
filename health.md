## Health Endpoint

The API health endpoint should not contain any infrastructure code. Typically, the health endpoint will be exposed in order for the load balancer to check the status of the server before sending requests to it. It's also important for the auto-scaling feature since an unhealthy node is supposed to be replaced with a healthy one.

Putting infrastructure dependencies however, might cause more issue on the health endpoint. If you place a database ping at the `health` endpoint, and someone changes the endpoint, it will keep restarting infinitely until the connection is restored.

The health of the infrastructure (database, external service etc) should not affect the instance that is running. If you want to test the database health, then plug a health endpoint on the database. There are a few instance where the server keeps restarting mainly because the db is down, and it affects the whole service, even those that do not depend on the db.


## Basic API Server Information

Should probably return more meaningful information for backend services at the index `/` endpoint:

```js
{
  "language": "go version 1.10", 
  "build_type": "docker" // or binary
  "build_date: "2018-07-11T10:12:33Z",
  "version": "4a2690e",
  "deployed_date": "2018-07-11T10:12:33Z"
}
```


This is great. I first need to find a way to write better articles.
Here are some improvements that I’ve done so far:
1. Add health endpoint

This allows us to check several facts:
- Uptime: how long has the server been alive, to indicate when the deployment was last done. Human readable relative time
- deployed_at: last deployment time
- Version: what is the current version of the system
- Revision: the git hash of the system

What’s next? Better monitoring
