## Blue-green Deployment

Blue-green deployment is a technique for releasing your application in a predictable manner with the goal of reducing any downtime associated with a release. It's a quick way to prime your app before releasing, and also quickly roll back if there are issues.

- you have green application running in production
- you deploy the blue application
- you test the blue application
- you switch the load balancer to point to the blue application

# Canary Releases

Canary releases are a way of sending out a new version of your app into production that plays the role of a "canary" to get an idea of how it will perform (integrate with other apps, CPU, memory, disk usage etc).
