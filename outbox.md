# Outbox Pattern

There is the transactional outbox and inbox patterns. They are meant to solve the issue of message deduplication to producer and consumer respectively.

Message broker normally implements at-least-once delivery, hence the responsibility of ensuring the message is processed exactly-once lies on the consumer instead.

The pattern for message deduplication on the consumer side is called `Idempotent Consumer pattern`.


Implementation:
1. store the message id in a table


```
while (true):
  msg = read()
  begin tx
    insert into inbox(id) values (msg.id)
    // Update one or more business object
  commit tx
  ack msg
```

2. store the message id in the business entities that it creates or updates
3. ensure the process is idempotent in the first place, e.g. creation is always idempotent, but updates or external side-effects (third-party API calls) may not be


## References

- https://softwaremill.com/microservices-101/
- https://martinfowler.com/articles/patterns-of-distributed-systems/idempotent-receiver.html
