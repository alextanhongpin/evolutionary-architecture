```go
package main

import (
	"errors"
	"fmt"
	"log"
	"math/rand"
	"net/http"
	"time"
)

func init() {
	rand.Seed(time.Now().UnixNano())
}

type BackoffPolicy struct {
	Millis []int
}

var DefaultPolicy = BackoffPolicy{
	// Too short - retry too early, which might result in failure.
	// Too long - client will be in the pending state.
	// How fast will it takes for the server to recover from a typical failure? Research on the average, or
	// collect metrics on the retry duration that succeeds.
	Millis: []int{0, 250, 500, 1000, 2500, 5000, 10000, 15000},
}

func (b BackoffPolicy) Duration(n int) time.Duration {
	if n >= len(b.Millis) {
		n = len(b.Millis) - 1
	}
	return time.Duration(jitter(b.Millis[n])) * time.Millisecond
}

func jitter(n int) int {
	if n == 0 {
		return 0
	}
	return n/2 + rand.Intn(n)
}

func main() {
	maxAttempts := 5
	var attempts int
	for {
		duration := DefaultPolicy.Duration(attempts)
		fmt.Println("sleeping for", duration.Seconds())
		time.Sleep(duration)
		if err := work(); err != nil {
			log.Println("error", err)
			attempts++
			if attempts > maxAttempts {
				break
			}
			continue
		}
		break

	}
	log.Println("terminating")
}

func work() error {
	return errors.New("error")
}

type Client interface {
	Do(*http.Request) (*http.Response, error)
}

type ClientFunc func(*http.Request) (*http.Response, error)

func (c ClientFunc) Do(req *http.Request) (*http.Response, error) {
	return c(req)
}

type Decorator func(Client) Client


// Don't use this in production. It seems like writing a retry is more complicated that I thought. See references:
func Retry(max int, policy BackoffPolicy) Decorator {
	return func(c Client) Client {
		return ClientFunc(func(req *http.Request) (res *http.Response, err error) {
			var attempts int
			for {
				time.Sleep(policy.Duration(attempts))
				res, err = c(req)
				if res != nil {
					defer res.Body.Close()
				}
        // TODO: Check the status code, res.StatusCode >= 500
				if err != nil {
					attempts++
					if attempts > max {
						break
					}
					continue
				}
				break
			}
			return res, err
		})
	}
}
```

## References

- https://stackoverflow.com/questions/54704420/how-to-retry-http-post-requests
- https://medium.com/@nitishkr88/http-retries-in-go-e622e51d249f
- https://github.com/hashicorp/go-retryablehttp/blob/master/client.go
- https://8thlight.com/blog/colin-jones/2018/09/18/microservices-arent-magic-handling-timeouts.html
- https://docs.microsoft.com/en-us/azure/architecture/best-practices/transient-faults
- https://blog.runscope.com/posts/phil-sturgeon-taking-a-timeout-from-poor-performance
- https://www.peterbe.com/plog/best-practice-with-retries-with-requests
- https://www.envoyproxy.io/learn/automatic-retries
