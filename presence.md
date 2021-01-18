# How to track user's online/offline/away status?

## Level 0

Using websocket:

1. When the user is online, find the user's friends that are online too, and publish the `online` status.
2. When the user goes offline, find the user's friends that are online too, and publish the `offline` status.

The approach above fails to take into account that the same user may login into different devices. So whenever the user is logged out from one of them, friends will be falsely notified.

Basically, to handle this, we need to keep track of the user's online session.

## Level 1

Everytime the user goes online, we keep track of the user's unique session id. The session id can be set in a distributed cache, like redis, and can be set with a TTL. In the application layer, we periodically run a heartbeat t0 to update the TTL, so the TTL must be longer than the TTL, e.g. t0 = 9/10 ttl. 

1. John goes online on the browser. A unique session id is generated, and the online session count for John is 1. 
2. John's friends are notified that John is online.
3. John goes online on the mobile. A unique session id is generated, and the online session count for John is 2.
4. John's friends are not notified, since they know he's already online.
5. John goes offline on the mobile. The online session count for John is 1.
6. John's friends are not notified that he's offline, because John's session on the browser is active.
7. John goes offline on the browser. The online session count for John is 0.
8. Notify John's friends that he's offline.
