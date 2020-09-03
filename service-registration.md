# Service Registration

In an organization, the tech team may be divided based on domains/features/groups etc, depending on the structure of the organization. Finding out what services exists, and how they connect with each other (dependency graph) is hard. 

To make services more discoverable, and identify their dependencies with other services, we need a way to register them.

## Why

The advantages of registering _active_ services, which means services that are running in production is essential for the following reason:

- makes it easier to create/depecreate old services
- migration could be planned better
- traceability of the service and their dependencies across different teams
- language independent, we don't need to care what technology is used, they just need to provide information on what kind of services they are and who they are calling/who is calling them.

## How


- Create a file, e.g. `json` data that stores information about the service for each _active_ service, and have a service that scrapes this from the repository (or when the service is running). 
- Expose an endpoint that contains the service endpoint and create a service that scrapes it. Similar to `/health`, `/metrics` or `/ping` endpoint, but for getting application service information. When the service did not respond in _n_ seconds, show the terminated service status. 

## What

The service should contain the following information:

- role
- owned by team
- created since 
- updated since
- last deployed
- name
- description
- version
- dependencies (calling who, who's calling)
- technology stack (vulnerable?)
- private (whether the service is active or not, or has been deprecated)
- deprecated at (a future date where the service might be deprecated)


A simpler approach is to use package namespace for services, e.g. github namespace, `alextanhongpin/service-a`, similar like go packages. 
