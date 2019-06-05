## Dealing with hotspot keys

For go, use groupcache.

- Other solution includes creating mysql slaves for read only and a master for write only.
- Scaling is about managing resources.
- Use singleflight to avoid thundering herd

https://www.alibabacloud.com/blog/redis-hotspot-key-discovery-and-common-solutions_594446
https://capotej.com/blog/2013/07/28/playing-with-groupcache/
https://gist.github.com/fiorix/816117cfc7573319b72d
http://talks.golang.org/2013/oscon-dl/groupcache.go
https://sconedocs.github.io/groupcacheUseCase/
