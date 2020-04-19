## Logging

See [here](https://github.com/alextanhongpin/ideas/blob/master/logging.md) too.

## Log Analysis

Log analysis seeks to make sense of logs generated from applications. Why do we need log analysis?

- compliance with security policies (any example?)
- compliance with audit or regulation
- system troubleshooting (how to detect fast?)
- forensic
- security incident response
- understanding online user behaviour (how?)

## Information from logs

- url path
- error messages
- request params/body/query strings and fragments
- HTTP status code (2xx-5xx)
- user agent
- client ip 
- timestamp
- HTTP method (GET, POST...)
- hostname
- local server ip
- bytes downloaded 
- latency/time taken
- request id


## Types of analysis

- top pages
- frequent errors
- country/city breakdown
- request count
- mobile first vs desktop
- speed


## Functions and technologies

More on this on [wiki](https://en.m.wikipedia.org/wiki/Log_analysis).

- pattern recognition
- normalization
- classification and tagging
- correlation analysis
- artificial ignorance


# Logging

How to write your own middleware to log http requests in golang.

https://onepage.nopub.io/p/Logging-HTTP-requests-in-Go-233de7fe59a747078b35b82a1b035d36

https://www.screamingfrog.co.uk/22-ways-to-analyse-log-files/



## What is the difference between logging and events?



https://answers.splunk.com/answers/771511/whats-the-difference-between-an-event-and-a-log.html

https://stackoverflow.com/questions/14127591/what-is-the-difference-between-events-events-logs
https://charity.wtf/2019/02/05/logs-vs-structured-events/



## How to perform log analysis?


https://coralogix.com/log-analytics-blog/analyzing-log-data-important/
https://digitalguardian.com/blog/what-log-analysis-use-cases-best-practices-and-more
https://sematext.com/blog/log-analysis/
https://builtvisible.com/log-file-analysis/


## Why do we perform log analysis
