There are mainly two ways of coordinating a saga:
- choreography - the saga's participants exchange event
- orchestration - a centralized orchestrator uses requests/asynchronous reply-style messaging to tell the participants what to do

For simple sagas, choreography suffice. For more complex sagas, orchestration is often easier to understand.

## Example

We have `Customer` and `Order` services. `Order` is created in the `OrderService` and credits are reserved in the `CustomerService`. 

- the `OrderService` will create an `Order`, then the `CreateOrderSaga` is created.
- `CreateOrderSaga` sends an asynchronous event to the `CustomerService` to reserve credit for the order. If the reservation fails, then the compensating transaction is carried out.
- If the credits have been reserved succesfully, then the Order will be approved.


