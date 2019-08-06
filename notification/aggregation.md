## Notification aggregation

Given a comment system, users can comment on a given post. Any users that comment on the system can receive a notification. When multipler users comment on a post, it should be shown as a single notification, instead of multiple individual notifications, which can be spammy. How would we design a system to do so?

Aggregate logic:
- we can aggregate the notification by batching them by a particular id (aggregate/group id)
- we can set a limit of the aggregate, e.g. send after 100 items are gathered first or when 60 seconds has elapsed
- we don't need to have real time capability
- additional: if user has viewed the comment, cancel the notification

Memory constraint:
- say we have 200 comments per seconds, if we clear them every 60 seconds, we will be holding 12,000 comments. If each of them holds 10 kb, we would be using around 100 mb of in memory storage.


```js
const time = {
  MilliSecond: 1000,
  Second: 1000,
  Minute: 1000 * 60,
  Hour: 1000 * 60 * 60,
  Day: 1000 * 60 * 60 * 24
}

function delay(duration = time.Second) {
  return new Promise((resolve) => setTimeout(resolve, duration))
}

class Aggregate {
  constructor(size = 3, ttl = time.Second) {
    this.timestamp = Date.now()
    this.value = []
    this.ttl = ttl
    this.size = size
  }
  canSend() {
    // Additionally, we can also check if the size is full or not.
    const sizeExceeded = this.value.length > this.size
    const timeExceeded = Date.now() > this.timestamp + this.ttl
    return sizeExceeded || timeExceeded
  }
  push(item) {
    // Update the timestamp.
    this.timestamp = Date.now()
    this.value.push(item)
  }
}

class Notification {
  constructor() {
    this.messages = {}
    this.worker = setInterval(this.process.bind(this), 2 * time.Second)
  }
  process() {
    for (let groupId in this.messages) {
      const aggregate = this.messages[groupId]
      if (aggregate.canSend()) {
        delete this.messages[groupId]
        console.log('process:', aggregate)
      }
    }
  }
  send(msg) {
    if (!this.messages[msg.groupId]) {
      this.messages[msg.groupId] = new Aggregate()
    }
    const aggregate = this.messages[msg.groupId]
    aggregate.push(msg)
    if (aggregate.canSend()) {
      delete this.messages[msg.groupId]
      console.log('sending:', aggregate.value)
    }
  }
}

async function main() {
  const notification = new Notification()
  const messages = Array(100).fill(() => ({
    groupId: Math.floor(Math.random() * 5),
    userId: Math.floor(Math.random() * 10),
  })).map(fn => fn())
  for (let msg of messages) {
    const duration = Math.round(Math.random() * 1000)
    await delay(duration)
    notification.send(msg)
  }
}

main().catch(console.error)
```

## Improvements

The solution above might work if we are just running a single server. But we might need to run additional servers for redundancy, so it would be better to store the aggregated in a distributed storage, say redis instead. 

- How do we prevent multiple servers from processing the same notification twice (data race)? 


