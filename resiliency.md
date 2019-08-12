

## Hotspot prevention/thundering herd

- using golang single flight and groupcache
- preventing thundering herd with nginx
- https://www.nginx.com/blog/mitigating-thundering-herd-problem-pbs-nginx/
- throttling
- rate limiting
- circuit breaker

## Collapsing Forwarding

We can also prevent thundering herd when populating cache with Collapsing Forwarding.

http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_cache_lock

Only one request at a time is allowed to populate a new cache element while other requests of the same cache element will either wait for a response to appear in the cache.

```
# Enabled collapsing forwarding
fastcgi_cache_lock on;
```


## Singleflight pattern

Look into the singleflight pattern for subscribing to connection. http://www.cs.duke.edu/courses/cps296.4/fall13/838-CloudPapers/dean_longtail.pdf. Also look into connection pooling in Golang, and sync.Once.


## References

- https://news.ycombinator.com/item?id=19683410
