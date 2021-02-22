# Automated Workflow Patterns

1. State machine
2. Process manager
3. Scheduler-agent supervisor
4. Saga (choreography and orchestration)
5. The routing-slip pattern


# Process Manager

- A process manager is build on top of a state-machine. 
- A process manager will retain state between messages, and will contain logic in order to determine which action should be taken in response to a message (for example, transitioning state or sending another message). 
- Some frameworks incorrectly refer to these as sagas.
- Process Manager is a processing unit that it's existing in order to mantain the state of the sequence and determine the next processing step based on intermediate results. It's a routing pattern. It's more like an Orchestrator Saga.

# Saga

- saga is a pattern intended to help manage failures
- it involves multiple workflows across systems, where each will allow some form of compensating action to be taken in a later transaction in the case of a failure elsewhere.
- This compensation is the defining characteristic of a saga. 
- Note that the saga itself does't know what the compensating action might be. 
- Sagas are often implemented using the routing slip pattern (choreography saga).
- saga has not state while process manager has
- a pattern that implements business transaction that spans multiple services 
- done by creating a sequence of local transactions where each local transactions updates the database and publishes a message or an event to trigger the next local transaction in the saga (look also at _Outbox Pattern_)
- There are two possible ways to implement a saga: Orchestration (an orchestrator tells the participants what local transactions to execute) and Choreography (each local transaction publishes domain events that trigger local transactions in other services).
- often designed as long living transactions where the required steps may not be known at design time 
- for each transaction, there's a compensating action

# References 
1. [Cloud Architecture: The Scheduler-Agent-Supervisor Pattern](https://vasters.com/archive/Cloud-Architecture-The-Scheduler-Agent-Supervisor-Pattern.html)
2. [Microsoft: Scheduler-agent-Supervisor](https://docs.microsoft.com/en-us/azure/architecture/patterns/scheduler-agent-supervisor)
3. [Powerful Patterns for developing Automated Workflows](https://dotnetsilverlightprism.wordpress.com/2015/01/04/powerful-patterns-for-developing-automated-workflows/)
4. [Microsoft: Process Manager](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/jj591569(v=pandp.10)?redirectedfrom=MSDN)
5. [Saga vs Process Manager](https://blog.devarchive.net/2015/11/saga-vs-process-manager.html)
6. [StackOverflow: What is the difference between a saga, a process manager and a document-based approach?](https://stackoverflow.com/questions/15528015/what-is-the-difference-between-a-saga-a-process-manager-and-a-document-based-ap)
7. [Clarifying the Saga Pattern](http://web.archive.org/web/20161205130022/http://kellabyte.com:80/2012/05/30/clarifying-the-saga-pattern)
8. [Process Manager](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)
