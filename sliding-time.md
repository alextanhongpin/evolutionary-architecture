What is the best way to track frequency of events in the given period?

1. to  find the number of events in in the last period **T**, find if the elapsed duration overlap
2. or check if the timestamp > current time - period
3. alternative is to round the time by the period, and increment the counter. The previous period can be cleared when a new one is created...

## Implementation 1
```
if now - timestamp > period:
  increment counter
```
## Implementation 2
Note that this is just a reverse of the formula above:
```
if now - period > timestamp:
  increment counter
```
## Implementation 3

With this solution, the timestamp is recorded at every epoch (e.g. every 5 seconds, beginning with seconds 0, 5, 10...).
```
add event:
  counter[timestamp - timestamp % period]++
  
getting event:
  counter[now - now % period]
```
