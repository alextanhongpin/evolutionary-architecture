# Designing a real-time chat

## Requirements

Functional Requirements:

- users can chat with another user that is in his contact list
- users can chat in a group with multiple users
- users can see the status of another user (online, offline, away)

Non-functional requirements:

- system should store the history of the chat
- system should be highly available

Extended requirements:

- when the user is offline, the user should receive a notification/email when there are new messages
- users should be able to send files/photos
- users should be able to send emoji/stickers

## Capacity Estimations and Constraints

**Traffic:** Let's start with a rough estimate. Let's say that we are expecting 500 messages per second, and the read to write ratio is 100:1 (there are more reads on the system than write), then our system's QPS (query per second) is:
```
Write QPS
Rate: 500 msg/s
1 day traffic: 43.2M/s
1 month traffic: 1.3B/s
1 year traffic: 15.6B/s

Read QPS:
Rate: 50k/s
1 day traffic: 4.32B/s
1 month traffic: 130B/s
1 year traffic: 1.56T/s
```



**Storage:** If we are planning to store the messages for 5 years:
```
1 year storage = 500 msg/s * 86400 * 30 * 365 * 5 * 140 bytes / 1024 ^ 4 = 301 TB
5 year storage = 1.5 PB
```

**Bandwidth:** Assuming that the messages are short, and does not exceed 140 characters:
```
1 char = 1 byte
Write per second: 68KB/s
Write per month: 1.3B/s * 140 bytes = 169.5 GB/s

Read per second: 6.8MB/s
Read per month: 1.7TB/s
```

**Memory:** Based on pareto principles, 80% of the read comes from 20% of the messages. We want to cache this data in the storage:

```
With 50000 msg/s, in one day:
50000 * 86400 * 500 bytes = ~2TB
20% * 2TB = 400 GB
```
We need 400 GB to cache 20% of the requests.

## APIs

What apis do we need?

```
sendMessage(from, to User, msg string)
broadcastMessage(from User, group Group, msg string)
joinGroup(id User, group Group)
leaveGroup(id User, group Group)
status(id User) bool
getHistory(id User, since Time, limit int)
notify(id User, msg string)
```
