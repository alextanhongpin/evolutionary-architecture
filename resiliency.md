

## Hotspot prevention/thundering herd

- using golang single flight and groupcache
- preventing thundering herd with nginx
- https://www.nginx.com/blog/mitigating-thundering-herd-problem-pbs-nginx/
- throttling
- rate limiting
- circuit breaker


## Singleflight pattern

Look into the singleflight pattern for subscribing to connection. http://www.cs.duke.edu/courses/cps296.4/fall13/838-CloudPapers/dean_longtail.pdf. Also look into connection pooling in Golang, and sync.Once.
