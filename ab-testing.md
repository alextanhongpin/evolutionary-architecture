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
- make the tests toggable (turn on or off)

## Sample AB, displaying the button blue or green

- we want to display the button blue or green to the end users
- depending on the button displayed, we want to track if the user click on them
- we can set a flag to display a value (blue or green) or hide them
- we need to identify which id range to show the color blue or green, let's say we use odd number for blue and even number for green
- we need to identify the period where the tests should be active, let's say we set it to 2 weeks to get sufficient numbers
- we need to store the metrics, which is the data whether the user click on the blue or green button
- we probably need a place to display the metrics, and the decision on the outcome, e.g. blue button perform n% better than green, so we should use blue
- basic algorithm to compute the results includes just basic counter, chi-test


## References
- http://blog.richardweiss.org/2016/12/25/hash-splits.html
- https://blog.offerup.com/a-good-hash-is-hard-to-find-60e8a201e8ce
- http://bytepawn.com/beautiful-ab-testing.html
- https://www.lucidchart.com/techblog/2016/06/22/ab-testing-pt-2/
- https://observablehq.com/@kalebdf/a-b-testing-deterministic-bucketing
- https://github.com/sampsyo/dtest/wiki/Hash-Function-Testing
- https://www.exp-platform.com/Documents/controlledExperimentDMKD.pdf
