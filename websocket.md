# WebSocket

How to scale WebSocket horizontally? Due to the nature of WebSocket, when a client disconnect and reconnect, it might not connect back to the same server.

To solve this, we can enable sticky session on the load balancer.

Alternatively, we can register the client connection through redis pub sub. The message would be broadcasted through pub sub to the server that the client connects to.


## Multiple clients

The same user can be connecting on mobile and desktop (multiple browser tabs). Hence using just the user id to identify the client would not work.


1. For each client, create a unique session id + user id
2. Register the server host with the unique key above
3. Subscribe to the server host, and execute the WebSocket emit when there are new messages
4. When publishing a message to a user, find the all the sessions that the user has, and find each server host
5. Publish the message to all those server host

```
# Subscribe to the server host
client.subscribe(hostname, ({ userId, message }) => {
  // Find the user from the list of websocket clients and broadcast the message.
})

# Redis key value
<user_id>:<session_id> = <server_host>

# Or use hash map
user_id: {
  session_id_1: server_host_1,
  session_id_2: server_host_2
}

# To find which server the user is connected to in order to publish the message:
const serverHosts = findRedisKeysAndValues(<user_id>:*)
for (let host of serverHosts) {
  client.publish(host, { userId, message })
}
```

Note: We do not need to know the server host, we can just publish to all server when broadcasting new messages - it would be noisy though.
