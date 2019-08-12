
# Rate Limit 

- improve application security and performance
- protect resources from abuse
- avoid thundering herd/DDOS

## Where to rate limit

- All endpoints
- For public GET endpoints, a typical request of 5 req/sec based on IP should be sufficient
- For POST/UPDATE/PATCH, limit it depending on your scenario. For example, register and login endpoint should be limited to prevent brute force attempts, and upload files endpoint to prevent resource exhaustion. 
- Database calls. Aside from caching, adding a rate limit to reduce the load can be effective too. Having a predefined quota for each service to call the database can be useful when there are many services calling the same database.

## Types of rate limit

- Rate-limit throttling: Allows a requests to pass through until a limit is reached for a time interval. 
- IP-level throttling: Make an API available only through whitelisted IP, or limit the number of requests sent by a certain client ip. Note that there can be many users under the same IP address.
- Scope limit throttling: Based on the classification of users, you can restrict access to certain part of the API (methods, functions/procedures) or features.
- Concurrent connection limit: Limit the number of connections a user can make.
- Resource level throttling (a.k.a Hard Throttling). If a certain query returns a large result set, you can throttle the request so that your sql engine limits the number of rows.
- Tiers of throttling 
  - api level
  - application level
  - user level
  - account level

## Design consideration 

- single machine, single threaded scenario
- single machine, multi threaded scenario (race conditions)
- distributed scenario (use distributed cache like Redis)
- client side (throttles network calls)

## Thoughts

- how to deal with delay from reading/setting the values in distributed system? Add additional time
- rate limiting and throttle are two different things for API Quotas. Rate limit is the rate at which the api may be consumed, normally requests per second, while throttle is the number of requests that can be made at the given time window. For example, our login api endpoint has a rate limit of 1 request per second (user can make up to 86,400 requests in a day), but it only has a daily limit of 10,000 requests per day. 

## Algorithms

- leaky bucket
- token bucket
- fixed window counter
- sliding window logs
- sliding window counters

## Fixed Window Counter

- create buckets for a time window (if it's 5 seconds, then the time window will be 00:00, 00:05, 00:10)
- each bucket holds a count
- space complexity: O(1) - storing the current window count
- time complexity: O(1) - get and simple atomic increment operation
- can be incorrect, take the 1 hr time window, 00:00, 01:00, 5 req/hr is not honored if we take the time from 00:30 to 01:30

## Sliding Window Logs

- store every timestamps for the events that happen
- when making a new request, purge all events that time older than the time window. This can be done lazily or in the background
- the new timestamp is appended to the user's queue
- check the length as the count
- space complexity: O(max requests seen in time window) - stores all timestamps of the requests in a time window
- time complexity: O(max requests seen in a window time) - deleting a subset of timestamps
- high memory footprint
- high time complexity for removing older timestamps
- works perfectly

## Sliding Window Counter

- hybrid of both fixed window counters and sliding window logs
- break time window into smaller buckets
- each bucket stores the request count
- space complexity: O(number of buckets)
- time complexity: O(1) fetch the recent bucket, increment and check against the total sum of buckets

Implementation of the various rate limiting algorithms here:

- https://github.com/alextanhongpin/go-learn/blob/master/ratelimit.md
- https://medium.com/@saisandeepmopuri/system-design-rate-limiter-and-data-modelling-9304b0d18250

## HTTP Headers

- X-RateLimit-Limit: The maximum number of requests available in the current time frame
- X-RateLimit-Remaining: The number of remaining requests in the current time frame
- X-RateLimit-Reset: A unix timestamp of the expected time when the rate limit will reset
- Retry-After: The date or delay in seconds for the next request

## References

- https://nordicapis.com/everything-you-need-to-know-about-api-rate-limiting/
- https://developer.ibm.com/articles/mw-1705-phillips/
- https://timoh6.github.io/2015/05/07/Rate-limiting-web-application-login-attempts.html
