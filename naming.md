## Naming Convention

- For entities, they must be noun. e.g. User
- For middleware, they can be noun ending in -er, -or or -ar to indicate some work being done.
- For packages, use nouns if possible. Don't use verb. E.g. log, as opposed to logger.
- For interface, use verbs, .e.g logger.

https://en.oxforddictionaries.com/spelling/nouns-ending-in-er-or-and-ar


## API Naming

This is especially useful. Come up with the verb convention for your application (include it as part of the cheat sheet):

https://cloud.google.com/apis/design/naming_convention

## AWS Infrastructure

Naming infrastructure can be tricky. There are several naming convention that we can adopt:

- per domain
- per team
- per category (using colors, fruits, pokemon name)



We might want to include the infrastructure naming too, like message bus, database, loadbalancer.

- different region
- different environment

## Naming Patterns (Strategy)

It is always useful to have naming convention to keep things consistent. In order to validate it, we can just tools to `lint` the names or commit messages to ensure that they are in the right format. A simple pattern such as validating a name is `a-zA-Z0-9` is good enough to keep naming consistent. Other useful naming conventions to pay attention too:

- api resources endpoint
- api versioning endpoint
- service/repository/controller verb endpoint
- css props, to differentiate them from state
  - Given a button, which props would make sense?
  - round or rounded? since it is a property style, it should be round
  - disable or disabled? since it is a state, it should be disabled
  - bold or bolded? since it is a property style, it should be bold
  - invert or inverted? since it is a property style, it should be invert
- different languages have different requirement for letter casing too - whether it's camel case, pascal case, snake case

Use examples to demonstrate them.

## References


- https://www.ibm.com/blogs/cloud-archive/2016/11/devops-microservices-architecture/
- https://www.device42.com/blog/2014/02/standardizing-host-and-server-naming-conventions/#:~:text=Your%20goal%20should%20be%20a,well%20here%2C%20like%20nh%2D%5D
- https://wikitech.wikimedia.org/wiki/Infrastructure_naming_conventions
- https://acloud.guru/forums/aws-certified-solutions-architect-professional/discussion/-K2uAI8brOBKCaAMClsW/anyone-have-a-naming-convention-standard
- https://confluence.huit.harvard.edu/display/CLA/Cloud+Resource+Naming+Conventions?desktop=true&macroName=livesearch#CloudResourceNamingConventions-AWSResourceNamingStandards
- https://www.cloudconformity.com/knowledge-base/aws/EC2/ec2-instance-naming-conventions.html
- https://www.threatstack.com/blog/101-aws-security-tips-quotes-part-3-best-practices-for-using-security-groups-in-aws
- https://www.hudl.com/bits/how-we-stay-sane-with-a-large-aws-infrastructure



