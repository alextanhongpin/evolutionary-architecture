# Using event driven architecture

Say we are building Quora. User can post questions. When someone answers the question, the owner, as well as other subscribers will be notified. We will design a notification system that could accomodate this. 

So far, we have identified the following important attributes:
- __actor__, the one performing the action that triggers the notification delivery
- __action__, the action being performed
- __object__, the subject of the action
- __event__, the result from the action

## Question

John (__actor__) created a question (__action__), which is reflected in the __question__ table:

| id | body | user | 
| -- | -- | -- |
| 1 | how to design a notification system? | john |

## Answer 

Alice answered John's question. The __question__ table is as follow:
| id | body | question_id | user |
| - | - | - | - |
| 1 | read this! | 1 | alice |

## Subscription

To keep track of who to send the notifications to, we introduce the concept of subscription. Whenever the user wants to be notified of an event, they first have to create a subscription to that event. When a user creates a question (__action__), they are subscribed to the notifications by default. Other users can optionally subscribe to receive notifications for the answers posted for the question too.
Since Alice posted an answer, Alice is automatically added to the subscription. Users can choose to disable them through preference settings. A basic __subscription__ table would look like this:


| id | entity id | entity type | topics | user | 
| -- | -- | -- | -- | -- |
| 1 | 1 | question | * | john |
| 2 | 1 | question | answer_created | alice |

## Event

Actions produces events, and they are stored in the __event__ table:

| id | aggregateid | aggregatetype | event | payload |
| - | - | - | - | - |
| 1 | 1 | question | question_created | `{"id": 1, "body": "how to design a notification system?", "user": "john"}` |
| 2 | 1 | answer | answer_created | `{"id": 1, "body": "read this!", "question_id": 1, "user": "alice"}` |

The application will have a background task pooling for events. The first event will be skipped since there's no one to notify (John should not notify himself). 

Processing the event `answer_created` follows the approach:
1. Find all subscribers for the question by `question_id`, excluding the sender (which is Alice in this case). In the __subscription__ table above, only `id=1` will be selected. Note that we need to handle the case where there's hundred, or million subscribers.
2. For each subscriber, create a new notification. The __notification__ table is as follow:

| id | sender | receiver | action | result| event | msg | sent_at | created_at |
| - | - | - | - | - | - | - | - | - | 
| 1 | alice | john | question:`{"id": 1, "body": "how to design a notification system?", "user": "john"}` | answer:`{"id": 1, "body": "read this!", "question_id": 1, "user": "alice"}` | question_answered | Alice answered your question "how to design a notification system?" | `null` | now |

Note that we store the action object, which is the question, and the result object, which is the answer together. This makes it easier to build the notification message. The disadvantage with this approach is, data may be stale (John deletes the question, or Alice deletes or updates the answer). If accuracy is important, then we should just store the ids and type of the action and result object, and query them when sending the notification.
The same logic applies for user, whereby we store the user identifier only, because user's profile is more prone to changes.

3. For each notification, perform delivery, and set the `sent_at` date.


## Aggregating Notification

In the above example, each notification is treated as an individual notification. What happens when Alice post multiple answers (note: by right, each question may only have one answer), or if we added a like feature which receive positive response (and aggresive clickers)? We can rollup the messages that belongs to the same `action` and delivered to the same `receiver`.

The __subscription__ table, adding in the like event as well as new answers. Alice is now subscribed to the answer created for likes events. Bob also subscribes to John's question, and Alice's answer:


| id | entity id | entity type | topics | user | 
| -- | -- | -- | -- | -- |
| 1 | 1 | question | * | john |
| 2 | 1 | question | answer_created | alice |
| 3 | 1 | answer | answer_liked | alice |
| 4 | 1 | question | * | bob |
| 5 | 1 | answer | * | bob |

Bob answered John's question, and liked Alice's answer. The __event__ table now looks like this:

| id | aggregateid | aggregatetype | event | payload |
| - | - | - | - | - |
| 1 | 1 | question | question_created | `{"id": 1, "body": "how to design a notification system?", "user": "john"}` |
| 2 | 1 | answer | answer_created | `{"id": 1, "body": "read this!", "question_id": 1, "user": "alice"}` |
| 3 | 1 | answer | answer_created | `{"id": 2, "body": "you can also take a look at this", "question_id": 1, "user": "bob"}` |
| 4 | 1 | like | answer_liked | `{"id": 1, "answer_id": 1, "user": "bob"}` |


Now, multiple notifications will be sent. Let's take a look at the first batch, when Bob answers John's question:

1. Find all subscribers for the question. In this case John (`id=1`) and Alice (`id=2`). Bob is excluded, since Bob is the builder...ops sender.
2. Two new rows are inserted into the __notification__ table:

| id | sender | receiver | action | result| event | msg | sent_at | created_at |
| - | - | - | - | - | - | - | - | - | 
| 1 | alice | john | question:`{"id": 1, "body": "how to design a notification system?", "user": "john"}` | answer:`{"id": 1, "body": "read this!", "question_id": 1, "user": "alice"}` | question_answered | Alice answered the question "how to design a notification system?" | 3m ago | now |
| 2 | bob | john | question:`{"id": 1, "body": "how to design a notification system?", "user": "john"}` | answer:`{"id": 2, "body": "you can also take a look at this", "question_id": 1, "user": "bob"}` | question_answered | Bob answered the question "how to design a notification system?" | `null` | now |
| 3 | bob | alice | question:`{"id": 1, "body": "how to design a notification system?", "user": "john"}` | answer:`{"id": 2, "body": "you can also take a look at this", "question_id": 1, "user": "bob"}` | question_answered | Bob answered the question "how to design a notification system?" | `null` | now |

3. Batch before sending. We can batch the notification that belongs to the same receiver, has the same event type and is targetting the same action. Without batching, this is what John sees:

```
Notification 1: Alice answered the question "how to design a notification system?" 
Notification 2: Bob answered the question "how to design a notification system?" 
```

With batching:

```
Notification 1: Alice and Bob answered the question "how to design a notification system?" 
```

To mark the notification as read, we now used the last created notification date. When user clicks on the notification, we update the `read_at` date (not shown here). 



For the scenario where Bob liked Alice's answer:

1. Find all subscribers for the answer. Only Alice will receive the notification.
2. Create the notification:

| id | sender | receiver | action | result| event | msg | sent_at | created_at |
| - | - | - | - | - | - | - | - | - | 
| 1 | alice | john | question: `{"id": 1, "body": "how to design a notification system?", "user": "john"}` | answer:`{"id": 1, "body": "read this!", "question_id": 1, "user": "alice"}` | question_answered | Alice answered the question "how to design a notification system?" | 3m ago | now |
| 2 | bob | john | question: `{"id": 1, "body": "how to design a notification system?", "user": "john"}` | answer:`{"id": 2, "body": "you can also take a look at this", "question_id": 1, "user": "bob"}` | question_answered | Bob answered the question "how to design a notification system?" | `null` | now |
| 3 | bob | alice | question: `{"id": 1, "body": "how to design a notification system?", "user": "john"}` | answer:`{"id": 2, "body": "you can also take a look at this", "question_id": 1, "user": "bob"}` | question_answered | Bob answered the question "how to design a notification system?" | `null` | now |
| 4 | bob | alice | answer: `{"id": 2, "body": "you can also take a look at this", "question_id": 1, "user": "bob"}` | answer_like:`{"id": 1, "answer_id": 1, "user": "bob"}` | answer_liked | Bob liked the answer "read this!" | `null` | now |



We can apply conditional unique constraints to perform aggregation:

1. With Postgres, we can add unique constraints with a where condition `CREATE UNIQUE INDEX stop_myc ON stop (col_a) WHERE (col_b is NOT null);`
2. The notification table can be modified to include two additional column, `group` and `history`. If the `group` column is true, then we can apply the unique constraints on receiver, action id/type and event id/type. The history column than keeps track of the grouped sender as well as the sender action/event payload. 
3. In postgres, we can slice an array column, so that the size will never grow beyond a certain limit.

## Preventing unbounded growth

- keep only notifications for n days
- keep only the last 1000 notifications
