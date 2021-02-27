## Dealing with hotspot keys


Frequent access of the same key in a partition is known as a __hotspot key__.

Common causes of hotspot keys [2]:
- The size of user consumption data is much greater than that of production data, and includes hot items, hot news, hot reviews, and celebrity live broadcasts.
- The number of request slices exceeds the performance threshold of a single server.

For go, use groupcache.

- Other solution includes creating mysql slaves for read only and a master for write only.
- Scaling is about managing resources.
- Use singleflight to avoid thundering herd

# References

1. [Mailgun: Golang’s Superior Cache Solution to Memcached and Redis](https://www.mailgun.com/blog/golangs-superior-cache-solution-memcached-redis/)
2. [Alibaba Cloud: Redis Hotspot Key Discovery and Common Solutions](https://www.alibabacloud.com/blog/redis-hotspot-key-discovery-and-common-solutions_594446)
3. https://capotej.com/blog/2013/07/28/playing-with-groupcache/
4. https://gist.github.com/fiorix/816117cfc7573319b72d
5. http://talks.golang.org/2013/oscon-dl/groupcache.go
6. https://sconedocs.github.io/groupcacheUseCase/
