## Chaos Testing

To increase resiliency, it is useful to test the infrastructure and code such as:

- external client calls (http, grpc, websocket)
- database connection (what happens when it is overloaded, or disconnected)
- message queue
- multi-step transactions
