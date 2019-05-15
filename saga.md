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

- the transaction/compensation step can be distributed. Each steps might live on a different service, complicating the execution flow
  - we can create another versioned service that manages all the transaction and compensation step
  - this service would listen to all the events broadcasted and manage the state
  - we can call this service an orchestrator
- the transactions must be executed in the correct order
- you cannot skip a step
- when restarting or retrying the execution, ensure the completed transaction is not repeated (this can happen if you restart the service)
- if the transaction is paused (say if the server is restarted while the steps is executed halfway), ensure that the subsequent/current steps can be retried
- the compensation must be executed in the opposite flow (stack)
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
- irreversible side-effects might be triggered by transaction (e.g. notifications, email delivery, invoice sent)
  - best to leave the side-effects at the very end of the saga, so that it will be executed last
- current state might not be dependent on previous state (e.g. one step is payment, another step is delivery). While the payment is required before delivery, none of the delivery state is related the payment state. They are only related by the delivery status, in which it must be successful.
- compensate to N-th step. Sometimes we do not need to compensate all the steps in the saga. By right, if there are no compensation required, it should be excluded from the saga transactions. If you happen to see any steps in between that has this property, remove it and redesign your saga.
- on the other hand, some transactions are irreversible (resource creation etc). In this scenario, we might not want to undo the whole thing exactly (deleting the resource back), but just changing the status of the resource created (inactive, set deleted_at date for example).
- compensation does not necessarily mean undoing the steps, it can be just updating the status or marking the steps as failure

See golang code sample [here](https://github.com/alextanhongpin/go-learn/blob/master/saga.md)

The golang last example is using a bitwise to keep track of the states. It is useful because the steps are usually ordered, and we need to be able to reverse the order easily. Also, we can easily set/unset the bit to indicate that an action has been executed (multiple set/unset for bitwise operations is idempotent too! setting twice/unsetting twice will always result in the correct end state). Checking if a state has been executed is simpler too. We can easily check if the bit has been set. 

## Keeping the states in mysql

If we are using the bitwise operators, we can store each step number as a column in the unsigned int. Getting the sum is just as simple as:

```sql
SELECT SUM(step) FROM saga WHERE aggregate_id = ?
```

We can also create denormalized columns to store the prev and current state, as well as the applied state to ease compensation:

| aggregate_version | aggregate | aggregate_id | prev | data | current | step | desc | steps |
| - | - | - | - | - | - | - | - | - |
| 1 | purchase_goods | abc | {...} | {...} | {...} | 1 | order_requested | 1 |
| 1 | purchase_goods | abc | {...} | {...} | {...} | 2 | order_verified | 3 |
| 1 | purchase_goods | abc | {...} | {...} | {...} | 4 | delivery_made | 7 |

NOTE: Here we are keeping the final computed steps to make it easier to rollback. There are many other ways to store the sequenced steps, such as [adjacency list](https://en.wikipedia.org/wiki/Adjacency_list) or [nested set model](https://en.wikipedia.org/wiki/Nested_set_model).

Note that we included the `aggregate_version`, it should contain the sequence of the valid steps that needs to be performed. We can create a reference table to store the sequence. This will make it easier to evolve the system. E.g. What if we are going to add another step called `payment_made` after `order_requested`? All the steps below needs to be increment to reflect the updated sequence. Keeping track of the sequences can be made easier if we version them. If a new sequence have been added on 1 January 2010, then:

```
version: v1
aggregate: purchase_goods
sequence: [order_requested: 1, order_verified: 2, delivery_made: 4]
valid_from: 2000-01-01
valid_till: 2010-01-01

version: v2
aggregate: purchase_goods
sequence: [order_requested: 1, payment_made: 2, order_verified: 4, delivery_made: 8]
valid_from: 2010-01-01
valid_till: 9999-12-31
```

## Ensuring data is not being tempered.

Linking states in database (or anywhere else) is tricky. It is hard to tell if the data has been modified by another user, causing discrepancy in state. How do we know that the state before has not been tempered by anyone? One way is for the next state to store the parent's md5 hash and to compare them / or query them:

```sql
SELECT * FROM target_table WHERE md5(data) = 'caa9a728f63d89450e3c682243fa5bfdsq';
```

Of course, this can be used to verify if the data has been tempered or not. It does not give any indication on what has changed since the last tempering, or who did it. In order to track the changes, it might be necesary to perform audit-logging or something similar. 

This also introduces another problem. If the parent can be updated, then the children hash must be recomputed. We can set a rule so that none of the rows can be updated, and can only be added. But then again, updating a parent will trigger a propagation to the children - all children rows that are dependent upon the row must be updated. It would have been easier to design the row to store additional columns that contains the previous state, not the hashed reference...

In case if we do create a new row for the parent, we still need to indicate that the previously created row is invalid. We can add two additional columns - valid from and valid to to indicate the validity of the parent. Only the latest parent will be used for the query. This will add a significant amount of complexity as well as burden to the developers that works on the system, since they need to understand the reasoning behind such complex query.

This concept could be similar to a linked-list, or blockchain blocks...


## Routing Slip

https://www.codit.eu/blog/the-routing-slip-pattern/
https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html
http://vasters.com/archive/Sagas.html
https://blog.bernd-ruecker.com/how-to-implement-long-running-flows-sagas-business-processes-or-similar-3c870a1b95a8
http://proto.actor/blog/2017/06/24/money-transfer-saga.html
https://blog.knoldus.com/microservices-and-the-saga-pattern/

## Code


```go
type Command interface {
	Sequence() uint
	Execute() error
	Compensate() error
}

// orderUpdate:success
// orderUpdate:error

// orderUpdateCancelled:success
// orderUpdateCancelled:error
```

Sequence is important because:
- steps should be executed sequentially
- steps should have expiration period (if the execution is not successful within a time period, drop it. This is especially true for entity with limited stock, e.g. when booking ticket seats etc, since it's concurrent it may no longer be available in the next step)
- steps cannot be skipped (jumping from the first step to the last step)
- steps cannot be executed twice (idempotent as possible), but how about retries
- steps can be versioned (v1, v2). Different version has different sequence that needs to be completed
- the server acting as the orchestrator cannot go down (!!!)


Execution:
- can return either success or error. Success will lead to the next sequence, failure will cause all the previous steps to be compensated
- the current state of the execution is stateful and needs to be stored somewhere. It should also be able to survive the restart of the server

Compensation:
- the compensation transaction cannot fail
- if the execution fails in the middle of the process, e.g. 3rd step out of 5 steps, then all steps before it needs to be compensated (unless if there are no side-effects)

References:
- https://docs.microsoft.com/en-us/azure/architecture/patterns/compensating-transaction
