## A/B Testing

- how can we conduct effective a/b testing
- how to use canary deployment for testing
- how to test by countries
- what are good hashing strategy to distribute the users for testing
- can we use bandit algorithm for testing?
- how to create an a/b testing framework


## Strategy

- select two (or more) features to compare
- decide on how to split the users (hash string ids into integers, then take odd/even, take user in id ranges, show to users by different time zones)
- decide which users should be in the denylists/allowlists (by ips etc)
- decide on which environment to perform tests, or which not to perform (usually in staging etc you still want to run the tests to see if the code is working)

## References
- http://blog.richardweiss.org/2016/12/25/hash-splits.html
- https://blog.offerup.com/a-good-hash-is-hard-to-find-60e8a201e8ce
- http://bytepawn.com/beautiful-ab-testing.html
- https://www.lucidchart.com/techblog/2016/06/22/ab-testing-pt-2/
- https://observablehq.com/@kalebdf/a-b-testing-deterministic-bucketing
- https://github.com/sampsyo/dtest/wiki/Hash-Function-Testing
- https://www.exp-platform.com/Documents/controlledExperimentDMKD.pdf
