## Saga

There's a huge interest to explore this pattern, even when not using microservice. There are often services that contains a multitude of steps that has multiple dependencies and needs to be tracked step by step. The hard part is that some of the steps may involve side-effects or mutates state (e.g. payment made, email sent, photos uploaded). 

Keeping track of this states can be hard, and replaying/redoing them may cause unwanted side-effects, mainly because the operation is not idempotent.

Example of services that are not idempotent includes:
- i/o operation, mutating data, calling external services, sending email
- changes state, such as creation or deletion of resources, when it is not intentional (payment record created even when it order is not successful)

How do we take this services, ensure that each steps can be tracked, and any error allows us to compensate the user with minimal damage or retry without unwanted side-effect?

## State Machines
How do we ensure states moves in one direction only? We can use a bit operator for this.

They can represent the state of the process that we are tracking, and the bit is set when the operation is successful.

One potential issues is that when we add a new operation in between them, hence invalidating previous states. One solution is to versioned our state machines operation (by keeping all the states for a particular version somewhere, say a persistent database).

We need to store a unique identifier for the resource operation (e.g. if it's a payment record, we can use the order id - it will always be unique to that particular purchase, and we can always keep track of the state).
The states can only move in one direction (e.g. purchase, payment_made, order_received, delivering, delivered).


The identifier should be used for operation that can be performed once only, e.g for order, you will have an order if for the purchase. This order involves multiple steps, and cannot be repeated. This order will expire after a certain time duration. This order requires a few steps to complete, starting from initialised

The state machines can usually be visualized as a table, with previous state, next state and the actions that triggers the state change:

| prev | next | action leading to transition |
| -    | -    | -      |
| initialised | checkout | user checkout item |
| checkout | payment made, cancelled | user made payment, user cancel payment |
| payment made | order received | ops validate payment |
| order received | delivering | ops send delivery order to courier |
| delivering | delivered | courier send order, user signed verification |
| delivered | - | the state machine ends here |

Each action can be stored in the database as a row (or stored in one column to save space). They can be read and converted into bits and the sequence can be analysed.

## Thoughts


Have a main orchestrator manage the sequence.
For example, an order delivery system.

- User purchase an item
- Set the status to checkout
- User pays an item.
- Check the previous state though the aggregate Id, if exist, Add it as a new entry.

The algo is as follow, select all by the same aggregate id, if the I’d exists, query all events. For each events, validate the steps. Ge the last next step. If the current step equals the last next step, check if the validity is okay. add it in. Once all the steps have been completed, purge the entries, as it should not be retried. How to deal with versioning? Store the version in the Database and query them. For different version, we may have different state machine acting. 

We can probably use sorted sets to sort the sequence. Then compare it.

## Dealing with long-living transactions in Saga

What if one of the steps are not immediate, say sendDelivery after payment is confirmed? If there are multiple steps behind, we might need to rollback all (or just some) steps to compensate. So the logic to rollback the transaction must be independent (hopefully idempotent) and can be invoked anytime. Some part of the system needs to keep track of all the previous compensating transaction steps too. 

- which part of the system should have the knowledge?
- how to deal with versioning?
- how to ensure if the execution happens only once, or is idempotent?
- how to ensure the sequence is correct?
- how to deal with failures when attempting to rollback?


## What are the unexpected characteristic of a saga?

To simplify, saga consist of many steps. Each step will have a transaction, `t` (an action that moves that state forward) and a compensation `c` (an action that will revert, or at best negate the previous state). If we have four steps in one saga transaction, then the steps will consist of `(t1, c1, t2, c2, t3, c3, t4, c4)`. But here's a catch:

- each steps might live on a different service, complicating the execution flow
  - we can create another versioned service that manages all the transaction and compensation step
  - this service would listen to all the events broadcasted and manage the state
  - we can call this service an orchestrator
- the transactions must be executed in the correct order
- the compensation must be executed in the opposite flow
- it's a stack implementation (first-in, first-out)
- both transaction and compensation should be idempotent. Best if we can ensure they are invoked only once.
- invoking the compensation multiple times should not result in undesired side-effects
- compensation cannot be invoked without invoking transaction first
- adding in another intermediate steps in between will break old logic (not backward compatible)
- swapping the steps in between will totally screw things up
- the transaction might be long-living - if a delivery took a month, the transaction can only be executed then. 
- compensation logic would require the knowledge of the change in state made by the transaction step
  - the orchestrator should know the states change made by each transaction
- what if one step is missing in between, leaving a gap. this could happen by accident, even though it should not. 
- irreverisble side-effects might be triggered by transaction (e.g. notifications, email delivery, invoice sent)
  - best to leave the side-effects at the very end of the saga, so that it will be executed last


See golang code sample [here](https://github.com/alextanhongpin/go-learn/blob/master/saga.md)
