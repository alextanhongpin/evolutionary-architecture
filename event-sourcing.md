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
