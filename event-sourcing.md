## Activity/Event Manager

We can create an event manager to track domain activities, which can also be represented as a Feed in the Ops page when needed. The Feeds gives indicator on `usecases` that occurs.

For activities, we use the naming convention `actor-resource-verb` in past tense, e.g.:
- AdminRoleModified
- PaymentRejected
- ReceiptEmailSent
- UserLoggedIn

| Stream 1 – Badly named events | Stream 2 – Well named events |
| - | - |
| AccountInserted |	AccountCreated |
| AccountUpdated |	FeeCharged |
| AccountUpdated |	MoneyWithdrawn |
| AccountUpdated |	AccountCredited |
| AccountDeleted |	AccountClosed |

- ID
- Type
- Actor
- Target
- Metadata - Request,Response
- CreatedAt (there's no updated at or deleted at)



## Notes

Commands are imperative
- Commands is a request for the system to perform an action that changes the state of the system
- Event describes something that has happened in the system, typically as a result of a command

Tracking state changes 
- We need to first create an initial state
- After that, we create subsequent commands that request the state to change
- The events is the resulting state that is changed
- We can reapply all the events to the initial state to get the resulting state



## Mistakes

One of the common mistakes I made is not creating the initial state. Logging only the modified state does not add value - the modified states needs to be applied to the original state in order to compute the final state. Hence without the initial state, it is hard to know what changes. Also, how do we tie the states together? Since they can happen at a different timestamp, the changes needs to be aggregated in order to apply the changes.

## References

- https://www.innoq.com/en/blog/domain-events-versus-event-sourcing/
