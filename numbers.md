# Typical Numbers

```js
1 byte = 8 bits  // The size of a char
2 - 4 bytes      // The size of integer
1 million int32 characters (8 bytes each) takes up 8MB. (Actually it should be 1024 * 1024 characters). How to proof it? See below.

1024 bytes = 1 kilobyte (K / Kb) 
1024 * 1024 = 1,048,576 bytes = 1024 kilobytes = 1 megabyte (M / MB)
1024 MB = 1 gigabyte (G / GB)
1024 GB = 1 terabyte (T / TB)
1024 TB = 1 petabyte (P / PB)

1 Kb = 1e3 byte
1 MB = 1e6 byte
1 GB = 1e9 byte
1 TB = 1e12 byte
1 PB = 1e15 byte

1 Kb to bytes = 1024 ^ 1
1 MB to bytes = 1024 ^ 2
1 GB to bytes = 1024 ^ 3
1 TB to bytes = 1024 ^ 4
1 PB to bytes = 1024 ^ 5
```

## Unit

```js
thousand = 1e3 / 1000
million = 1e6 / 1,000,000
billion = 1e9 / 1,000,000,000
```

## Time

```js
1 hr = 60 * 60 = 3600 seconds
1 day = 24 * 3600 = 86,400 seconds
1 month (30 days) = 720 hours = 2,592,000 seconds
```

## Capacity Estimation and Constraints

Typical system design questions involves solving the _Fermi Problem_ through **Fermi Questions** or **Back-of-the-Envelope Calculations**.

- Traffic estimate
- Storage estimates
- Bandwidth estimates
- Memory estimates

Example with url shortener. Assumption, read and write ratio is 100:1, and we have 500M new URL shortening per month (write). So:

### Traffic Estimate
```
ratio read:write is 100:1
50B read:500M write 

Query per Second (QPS) for a month:
1 month (30 days) = 30 * 24 * 3600
Write QPS: 500e6/(30 * 24 * 3600) = 192.9012345679 ~200 URLs/s
Read QPS: Write QPS * 100 = ~19 K/s
```
### Storage Estimate
```
Assume we are going to store the URLs for 5 years. Since we estimate 500M URLs per month, and we assume each of them will take 500 bytes:
500M * 12 months * 5 = 30B (30e9)
30 billion * 500 bytes = 30e9 * 500 = 15e12 bytes
Since 1 TB = ~ 1e12 bytes:
That leaves us with ~15 TB (15e12) total storage
```

## Bandwidth Estimate

```
For every write requests, since we expect 200 new URLs per second, the transfer would be:
200 URLs/s * 500 bytes = 100,000 bytes
Total incoming data is: ~100 KB/s

For read requests, we expect ~19 K/s URL redirection, which makes it:
19e3 * 500 = 9,500,000 bytes
Total outgoing data is: ~9.5 MB/s
```

## Memory Estimates

```
To cache hot URLs, we follow the 20-80 rule (pareto's principle), meaning 20% of URLs generate 80% of traffic.
Since we have 19K requests per second, in one day, we would have:

19e3 * 3600 * 24 = 1,641,600,000
~1.7 billion requests

To cache 20% of this, we would need at least 170 GB of memory:
0.2 * 1.7e9 * 500 bytes / 1e9 (1GB) = ~170 GB
```

To summarize:
```
Write: 200/s
Read: 19K/s
Incoming data: 100 KB/s
Outgoing data: 9MB/s
Storage for 5 years: 15TB
Memory for cache 170GB
```

# Proving that 1 million (1024 * 1024) int32 is 4MB and, 1 million int64 is 8MB

```go
package main_test

import "testing"

func TestInt32(t *testing.T) {
	size := 1024 * 1024
	nums := make([]int32, size)
	for i := 0; i < size; i++ {
		nums[i] = int32(i)
	}
}

func TestInt64(t *testing.T) {
	size := 1024 * 1024
	nums := make([]int64, size)
	for i := 0; i < size; i++ {
		nums[i] = int64(i)
	}
}
```

Output: 

```
# Create memory profile
$ go test -memprofile mem.out

# Run profiling
$ go tool pprof mem.out
(pprof) alloc_space
(pprof) top10
Showing nodes accounting for 12MB, 100% of 12MB total
      flat  flat%   sum%        cum   cum%
       8MB 66.66% 66.66%        8MB 66.66%  github.com/alextanhongpin/go-test_test.TestInt64
       4MB 33.34%   100%        4MB 33.34%  github.com/alextanhongpin/go-test_test.TestInt32
         0     0%   100%       12MB   100%  testing.tRunner
```

## References
- https://hostingfacts.com/internet-facts-stats/
- http://highscalability.com/

