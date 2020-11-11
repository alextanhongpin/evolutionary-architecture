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


User ID:Session id = server host
