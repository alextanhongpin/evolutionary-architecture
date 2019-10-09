
## Configuration

- there are differences between configuration and secrets - one is meant to customise the application capability (e.g. feature flags, timeout, feature toggle/foggle), another is meant for security (credentials, db connection) 
- configs can be further categorised as static or dynamic. In most cases the config will be static - there’s no need to modify it (there are exceptions, e.g. to enable debug mode in production, allow certain log level, disable an endpoint or feature etc). But safe to say most config are static and requires a restart of the application in order for it to take affect.
- config properties includes the data type (environment variables are read as string), whether it is required, the default values etc. Care needs to be taken to ensure the right variables are passed in.
- a config’s purpose should best be documented
- configuration is different than annotation or labels - we may choose to annotate our application with a version or git hash, deployed date, created at date.

## Best practices

- centralized config
- initialize application config in one place
- for dynamic config, pass them through environment variables, or use a key-value store to toggle config


### Config

Config can be divided to a few categories:
- application config, prefixed with `APP_`, e.g. APP_VERSION, APP_HOST, APP_PORT
- infrastructure config, like database or message queue, e.g. DB_HOST, NATS_HOST, etc
- service config, for feature toggle, crontabs, etc
- dependencies, such as logger settings in different environment

When you have a lot of config, things can get pretty messy. Some of the key pointers for each config is to:

- document the usage
- make the source explicit (e.g. where to find them)
- set defaults

Configs are typically centralized in a single file. Why not break them down instead to different files or place them in their respective module folder?


## References

Also look into 12 factor app.

# Config and secret management

Static config vs dynamic config. Most configs are static. They are defined in the environment variables and is initialised once when the application start. 

1. Ensure that the env vars are parsed back into the correct data type, since they are mostly loaded as string.
2. Define the required and optional environment variables, as well as a short description on why they are needed (no magic vars).
3. Provide default values that allows minimal configuration.

Config for describing the app
- port, hostname, version
- Feature toggles
- Deployed at date
- Build date for docker
- GitHub repository path (not necessary)

Secret for credentials
- app secret
- Database credentials
- Tokens, jwt secret
