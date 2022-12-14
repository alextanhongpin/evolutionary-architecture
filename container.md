# Alternatives to docker for building/managing containers

- `containerd`: use to run containers. `containerd` cannot be used to build images
- `podman`: similar to `containerd`
- `buildah`: used to build container


## Best practices

- For graceful termination, use `exec` form, not `shell` form to ensure the termination signal is send to the correct process.
- Use distroless [^1] as alternative to `alpine` or `scratch`
- when running certain processes, use `tmpfs` in `docker-compose` to persist the data in memory. Could be used to speed up integration testing for `postgres`.

References
- https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

[^1]: https://github.com/GoogleContainerTools/distroless
