## Health Endpoint

Consider the following scenarios:

- You are working on an integration of an api, and you are given an API endpoint, `https://api.abc.com/`. Now, they did not provide sufficient information on the endpoint, so you figured you will have to take a look at the repository and read the code (assuming it's a different team from the same company). But you have no clue on how to get there.
- Someone reported a bug in the response api - you took charge of finding and fixing the bug. Your teammate shared that he has fixed the bug and had committed it to the repository. Now both of you are wondering, has the fixed been deployed to production?

Wouldn't it be better if our microservice is self-describing, and provide sufficient information that allows us to deal with the scenario above?

You go to the health endpoint and see the following:

```
GET /health HTTP/1.1
{
  "repo": "abc/api",
  "revision": "2963648",
  "version": "1.2.0",
  "deployed_at": "Wed Oct 02 2019 14:19:52 GMT+0000 (Coordinated Universal Time)",
  "uptime": "4 days"
}
```

Now, you know that you can find the code in repository `abc/api`. Also, you can check the last git revision as well as the version that is deployed to the current environment. From the uptime, you now know that no one has deployed the code in the last 4 days. With that, you have more insights on the services that are currently running.


### What is a health endpoint?

Typically, the health endpoint is exposed for the load balancer to check the status of the server before sending requests to it. If the load-balancer detects that a particular server is unhealthy, they would replace the server with a healthy one by spinning a new instance. Above, we are using the same endpoint to provide basic information of the server.

## Basic API Server Information

There are several other information that we could return. Here is an explanation on the response that we returned above, and some other possible additions:

- uptime: how long has the server been alive, to indicate when the deployment was last done. Human readable relative time.
- deployed_at: last deployment time, which is the date the server starts running. 
- version: what is the current version of the system
- revision: the git hash of the system
- build_type: The type of build, e.g. docker, binary
- build_date: The date the build was made, especially useful for docker. 
- language: The version of the language used, e.g. `go version 1.10`, note that this might introduce security risk if the particular version has security vulnerabilities.
- environment: e.g. staging, production etc. Normally this can be derived from the subdomain (`staging.abc.api` for staging, `abc.api` for production).


Are you using this too for your team? What do you think can be improved on this?

