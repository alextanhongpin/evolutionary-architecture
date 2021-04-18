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


## Change request system for database

- user submit request to modify data
- user defines when the data should change
- system store the command
- user can choose to cancel the command
- when the time range hits, the system updates the data in the storage

# approver define single step workflow

- approver create a new approval workflow
- approver define the fields that are required
- approver define who can be the requesters (through email)
- approver save workflow

# approver define multi step workflow
- approver create a new approval workflow
- approver add step 1
- approver define the fields that are required
- approver define who can be the requesters (through email)
- approver repeat for the number of steps
- approver save workflow

# Requested request approval
- requester view all the list of workflow available
- requester choose the desired workflow
- requester submit the relevant details
- system send notification to the approver

# Approver reject request
- approver view the notification
- approver click reject with comment
- system send notification to the requester


# Approver approve request
- approver view the notification
- approver click approve with comment
- system sends notification to the requester
- if the step is multi-step, the next series of requesters will be notified
