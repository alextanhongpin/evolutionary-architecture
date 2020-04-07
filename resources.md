## Difference between kubernetes labels and annotation

Kubernetes labels allow you to identify, select and operate on Kubernetes objects. Annotations are non-identifying metadata and do none of these things.

## Adding labels for resources

Apply the labels for every resource object:

| Key | Descriptinon | Example |
| - | - | - |
| app.kubernetes.io/name | The name of the application | mysql |
| app.kubernetes.io/instance | A unique name identifying the instance of an application | wordpress-abcxyz |
| app.kubernetes.io/version | The current version of the application (e.g. a semantic version, revision hash, etc) | 5.7.21 |
| app.kubernetes.io/component | The component within the architecture | database |
| app.kubernetes.io/part-of | The name of a higher level application this one is part of | wordpress |
| app.kubernetes.io/managed-by | The tool being used to manage the operation of an application | helm |

Note that this can be applied for any other non-kubernetes projects, e.g. for aws, just replace `app.kubernetes.io` with `app.elasticbeanstalk` etc.


Other cluster labels:

- team or cost center cluster labels: e.g. `team:research`, `team:analytics`. This label can be used for cost accounting or budgeting.
- component cluster labels: `component:frontend`, `component:redis`, `component:ingest`, `component:dashboard` (cron, message queue, load balancer?)
- environment or stage cluster labels: `environment:dev`, `environment:production`, `environment:qa`, e.g. see below for standardization of the environment naming. using the prefix `stage` is acceptable too, but standardize. environment is more predictable.
- state cluster labels: `state:active`, `state:readytodelete`, `state:archive`
- release labels: `release:stable`, `release:canary`
- tier: `tier:frontend`, `tier:backend`, `tier:cache`
- track: `track:daily`, `track:weekly`
- partition: `partition:customer-a`, `partition:customer-b`
- customer-facing: yes, no (this could be inside annotations)

References: 
- https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/
- https://cloud.google.com/kubernetes-engine/docs/how-to/creating-managing-labels
- http://docs.containership.io/kubernetes/kubernetes-objects/labels-selectors
- https://www.replex.io/blog/9-best-practices-and-examples-for-working-with-kubernetes-labels
- https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/

## Adding annotations


References:
- https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/

## Naming developer environments

  Note: When there are several synonyms for a term, we tend to highlight the first one to signify that it is the standardize convention (ubiquituous language) we are working with
  
For `resources`/application instances:
- dev
- qa
- staging
- prod

For `.env` extension
- .env.development
- .env.staging
- .env.production

References:
- https://cloud.google.com/appengine/docs/standard/java/creating-separate-dev-environments



## AWS Resource naming conventions practices
Have proper naming conventions for the Amazon Web Services security group. The naming convention should follow enterprise standards. For example it can follow the notation: ‘AWS Region+ Environment Code+ OS Type+ Tier+ Application Code’

```
Security Group Name – EU-P-LWA001
AWS Region ( 2 char ) = EU, VA, CA etc
Environment Code (1 Char) = P-Production , Q-QA, T-testing, D-Development etc
OS Type (1 Char)= L -Linux, W-Windows etc
Tier (1 Char)= W-Web, A-App, C-Cache, D-DB etc
Application Code ( 4 Chars) = A001
```
