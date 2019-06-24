# ETL, Change data capture (CDC), Stream Processing, and Complex Event Processing

CQRS and event sourcing is in another separate file.


## Stream Processing

https://wso2.com/library/articles/2018/02/stream-processing-101-from-sql-to-streaming-sql-in-ten-minutes/
https://medium.com/stream-processing/what-is-stream-processing-1eadfca11b97
https://www.toptal.com/mobile/context-aware-apps-and-complex-event-processing

https://calcite.apache.org/docs/stream.html

## Elasticsearch Syncing with MySQL and MySQL Migration

Utilise CDC (change data capture) to sync data from MySQL/Postgres to Elasticsearch. Check to ensure how they handle schema changes (removal/addition of new columns) and creation of new tables as well as deletion.

If there are constraints, set the rule to allow only addition, not removal of columns. Deprecate it and warn end users about it. If there are fields that are rapidly changing but does not require indexing, store the data as a json object in a new column. Else, just use nosql.

To prevent users from performing addition/deletion, create a new user and limit the access.



## Reactive System

Most of the time, we build applications hoping for users to interact with them. But what if we can bring the interaction out of the application? Take for example:

- push notification to notify when you are around somewhere (beacon/geolocation)
- notification to notify when a weather is about to change/earthquake detection
- an app senses that you are in danger and make a call/message others
- detect that a train is reaching soon 
- notify when you are close to a destination



## Complex event processing/context aware application

Real-time machine learning/fraud detection

- https://www.toptal.com/mobile/context-aware-apps-and-complex-event-processing
- https://www.kdnuggets.com/2015/11/petrov-real-time-machine-learning.html
- https://stats.stackexchange.com/questions/376677/how-to-frequently-update-classification-model-with-new-training-data
- https://medium.com/codait/keeping-your-machine-learning-models-up-to-date-f1ead546591b
- https://www.oreilly.com/ideas/lessons-learned-turning-machine-learning-models-into-real-products-and-services
- https://dzone.com/articles/real-time-machine-learning-with-tensorflow-in-data
- https://towardsdatascience.com/real-time-streaming-and-anomaly-detection-pipeline-on-aws-cbd0bef6f20e
- https://towardsdatascience.com/real-time-anomaly-detection-with-aws-c237db9eaa3f
- https://databricks.com/session/real-time-anomaly-detection-with-spark-ml-and-akka
- https://dzone.com/articles/creating-real-time-anomaly-detection-pipelines-wit
- https://engineering.salesforce.com/how-we-built-an-automated-anomaly-detection-system-onto-a-streaming-pipeline-84ecfd6420e0
- https://www.information-age.com/anomaly-detection-123475833/
- https://engineering.linkedin.com/blog/2018/09/automated-fake-account-detection-at-linkedin
- https://www.datadoghq.com/blog/alerting-101-metric-checks/
- https://comtradedigital.com/fraud-detection-monitoring/
- https://en.m.wikipedia.org/wiki/Data_analysis_techniques_for_fraud_detection
- https://precognitive.io/2017/10/12/using-behavioral-analytics-detect-ecommerce-fraud/
- https://precognitive.io/behavioral-analytics/
- https://precognitive.io/device-intelligence/
- https://en.m.wikipedia.org/wiki/Data_analysis_techniques_for_fraud_detection


Concurrent queue workers in golang. Can golang channels replaces queue?

Not really, we can make it persistent with level db (local cache) and reload the data back into the channels when the application restarts)
https://nesv.github.io/golang/2014/02/25/worker-queues-in-go.html
https://codeburst.io/diving-deep-into-the-golang-channels-549fd4ed21a8
https://www.opsdash.com/blog/job-queues-in-go.html
https://pusher.com/tutorials/messaging-queue-node-go
https://medium.com/@magicpineng/in-depth-look-at-a-scalable-robust-data-stream-processing-pipeline-using-golang-processing-500k-9e68310a0675
https://ewanvalentine.io/microservices-in-golang-part-5/
https://blog.wallaroolabs.com/2018/01/go-go-go-stream-processing-for-go/


## Distributed system design
https://medium.com/@shijuvar/building-distributed-systems-and-microservices-in-go-with-nats-streaming-d8b4baa633a2
https://blog.wallaroolabs.com/2018/01/go-go-go-stream-processing-for-go/


## Screaming architecture
https://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html
https://dzone.com/articles/screaming-architect
https://javadevguy.wordpress.com/2017/11/15/screaming-architect/
http://www.plainionist.net/Implementing-Clean-Architecture-Scream/
https://www.codingblocks.net/podcast/clean-architecture-make-your-architecture-scream/
https://www.freecodecamp.org/news/a-quick-introduction-to-clean-architecture-990c014448d2/
https://www.linkedin.com/learning/java-ee-design-patterns-and-architecture/what-is-screaming-architecture

## Event driven architecture
https://www.confluent.io/blog/data-dichotomy-rethinking-the-way-we-treat-data-and-services/
https://www.confluent.io/blog/journey-to-event-driven-part-2-programming-models-event-driven-architecture


## Change data capture event streaming
https://medium.com/myheritage-engineering/achieving-real-time-analytics-via-change-data-capture-d69ed2ead889





## Change Data Capture CDC 

Look into how to use change data capture to capture facts (data that has been successfully stored in the database.
This is perhaps a better (?) solution than streaming the results once the operation completed at the application layer because there can be some fields (autogenerated id from database) that cannot be obtained from the application layer.
(?) but if the insert fail, we might still need to publish the events saying that the operation failed.
- but at least we don’t need to deal with changes from the application side
- how do we handle schema changes in the database? or addition of new tables?


https://debezium.io/blog/2016/08/02/capturing-changes-from-mysql/
https://vladmihalcea.com/a-beginners-guide-to-cdc-change-data-capture/
https://medium.com/fundbox-engineering/https-medium-com-fundbox-engineering-diy-cdc-pipeline-from-mysql-to-snowflake-32c14b705cfe
https://medium.com/myheritage-engineering/achieving-real-time-analytics-via-change-data-capture-d69ed2ead889
https://www.percona.com/blog/2016/09/13/mysql-cdc-streaming-binary-logs-and-asynchronous-triggers/
https://vladmihalcea.com/how-to-extract-change-data-events-from-mysql-to-kafka-using-debezium/
https://engineeringblog.yelp.com/2016/08/streaming-mysql-tables-in-real-time-to-kafka.html
http://shzhangji.com/blog/2017/08/12/extract-data-from-mysql-with-binlog-and-canal/
https://www.bouvet.no/bouvet-deler/utbrudd/a-simple-todo-application-a-comparison-on-traditional-vs-cqrs-es-architecture
https://medium.com/@pierreprinetti/event-sourcing-in-go-the-event-handler-29f9438c58f0
https://medium.com/@shijuvar/building-microservices-with-event-sourcing-cqrs-in-go-using-grpc-nats-streaming-and-cockroachdb-983f650452aa
https://outcrawl.com/go-microservices-cqrs-docker/




## Example streaming
- store the last N values in the given time window
- when there are new values that exceeds the size N, remove the head and push to the array
- compute the last activity
```
E.g. when checking the user’s location
user {
	lat, lng, timestamp
}
- skip zeros/invalid location lat/lng
- we don’t need to store all the values, just compare the previous value with the latest one
- delta distance / delta timestamp > in valid range
- user has moved to an invalid duration
```

## Builing a streaming pipeline

Also look into service oriented architecture SOA.

## Use content enricher concept
- also, rather than real-time recommendation, store the results of the recommendation before sending them out. Use streaming to ensure that the data can be replayed


## Message Queue

https://www.codemotion.com/magazine/kai-wahner-build-a-scalable-infrastructure-with-apache-kafka-1247
https://cloudblogs.microsoft.com/opensource/2018/07/09/how-to-data-processing-apache-kafka-spark/
https://hackernoon.com/distributed-log-analytics-using-apache-kafka-kafka-connect-and-fluentd-303330e478af?gi=81e296c44344
https://www.confluent.io/blog/using-apache-kafka-drive-cutting-edge-machine-learning
https://blog.rapid7.com/2013/12/18/5-uses-for-log-data-that-you-never-thought-of/
https://www.fastly.com/blog/7-business-uses-for-logging


