Creating approval system use cases
https://docs.microsoft.com/en-us/flow/sequential-modern-approvals


## Thoughts


- Actions: what can be done, who can do what (permission)
- Triggers: What affects the status changes? Can it be external events, a person etc?
- Idempotency
  - Can the state be forward only? Undo?
  - Can the state be reversed or cancelled.
  - Can the state be repeated? E.g. multiple approval different person
  - Can the state expire?
  - Can the state be handled in the future? Due date
- Does it need approval from one, two, many
- Is it sequential approval, or can it be simultaneous
- Roles: Who has higher priority, and who has more access rights



## Workflow approval system

- each workflow step is configurable
- workflow input should not depend on previous step output
- each workflow step can be described as json
- each step must have a set of required condition defined by approver
- when the step condition is fulfilled, approver received the notification
- once approver approved, the next step is executed

each step condition
- validator
- participants (email)
- approver
- required fields
- remarks 
