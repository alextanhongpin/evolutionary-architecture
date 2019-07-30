## Architecting for scale


- __Reliability__: The ability of your system to perform the operation it is intended to perform without making a mistake
- __Availability__: The ability of your system to be operational when needed in order to perform those operation

## How to improve reliability?
- use strongly typed language rather than dynamic language like JavaScript/Python on the server-side. They have the advantage that you do not need unnecessary type-checking. 
- handle validation for types, boundaries and business logic. Use JSON Schema for example to ensure the JSON is validated
- write tests for the happy/sad path

Software reliability techniques
- verification and validation
- trigger, locate and remove software defect
- measure and analyze
- trending reliability
- predictive reliability

## How to improve availability?

Redundancy does not guarantee availability
