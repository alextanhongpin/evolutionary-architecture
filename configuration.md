
## Configuration

- there are differences between configuration and secrets - one is meant to customise the application capability (e.g. feature flags, timeout), another is meant for security (credentials, db connection) 
- configs can be further categorised as static or dynamic. In most cases the config will be static - there’s no need to modify it (there are exceptions, e.g. to enable debug mode in production, allow certain log level, disable an endpoint or feature etc). But safe to say most config are static and requires a restart of the application in order for it to take affect.
- config properties includes the data type (environment variables are read as string), whether it is required, the default values etc. Care needs to be taken to ensure the right variables are passed in.
- a config’s purpose should best be documented
- configuration is different than annotation or labels - we may choose to annotate our application with a version or git hash, deployed date, created at date.


## References

Also look into 12 factor app.
