# Seven steps for system design interviews


- **Step 1: Requirements clarification:** Always ask a question to find the exact scope of the problem you are solving
- **Step 2: System interface definition:** Define what APIs are expected from the system. This will also ensure you haven't gotten any requirement wrong.
- **Step 3: Back-of-the-envelope estimation:** Also called the Fermi problem, it's good to always have a good estimate on the scale of thes system you are going to design
- **Step 4: Define data model:** This helps you decide on how data are partitioned.
- **Step 5: High-level design:** Block diagrams to represent core components of the system.
- **Step 6: Detailed component design:** Dig deeper into 2-3 components 
- **Step 7: Bottlenecks:** Try to discuss as many bottlenecks as possible - and find ways to mitigate them. Note that sometimes it is impossible to mitigate them, so you need to understand what is the tradeoff of the approach you choose.


## Gathering Requirements

According to wiki, the process of defining, maintaining and documenting requirements is called [requirements engineering](https://en.wikipedia.org/wiki/Requirements_engineering). To simplify things, we focus on gathering _functional_ and _[non-functional](https://en.wikipedia.org/wiki/Non-functional_requirement)_ requirements. We use the shortform **FR** for functional requirements and **NFR** for non-functional requirements:

Here's the difference:
- FR define _what a system is supposed to do_, NFR _how a system is supposed to be_.
- FR "system shall do <requirement>", NFR "system shall be <requirement>"


## Capacity Estimations and Constraints
