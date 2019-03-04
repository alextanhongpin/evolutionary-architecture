## Example Use Case

| Topic | Description |
| - | - |
| Title | Register for courses |
| Description | Student accesses the system and views the courses currently available for him to register. Then he selects the courses and registers for them. |
| Primary Actor | Student |
| Preconditions | - Student is logged into system <br/> - Student has not previously enrolled or registered <br/> - Student cannot register within 5 days of course start date |
| Postconditions | Student is registered for courses | 
| Main Success Scenario | 1. Student selects "Register New Courses" from the menu. <br/> 2. System display list of courses available for registering. <br/> 3. Student selects one or more courses he wants to register for. <br/> 4. Student clicks "Submit" button. <br/> 5. System registers student for the selected courses and displays a confirmation message. | 
| Extensions | - (2a) No courses are available for this student <br/> 1. System displays error message saying no courses are available, and provides the reason and how to rectify if possible. <br/> 2. Student either backs out of this use case, or tries again after rectifying the cause. <br/> - (5a) Some courses could not be registered. <br/> 1. System displays message showing which courses were registered, and which courses were not registered along with a reason for each failure. <br/> - (5b) None of the courses could be registered. <br/> 1. System displays message saying none of the courses could be registered, along with a reason for each failure. |




# References

Use Cases:
- https://fullstackmark.com/post/11/better-software-design-with-clean-architecture
- http://www.eg.bucknell.edu/~cs475/F04-S05/useCases.pdf
- https://en.m.wikipedia.org/wiki/Use_case
- https://creately.com/blog/diagrams/use-case-diagram-guidelines/
- http://tynerblain.com/blog/2007/01/22/how-to-write-good-use-case-names/
- http://tynerblain.com/blog/2007/04/09/sample-use-case-example/
- http://www.agilemodeling.com/artifacts/systemUseCase.htm#Figure1
- ftp://ftp.software.ibm.com/software/rational/web/whitepapers/RAW14023-USEN-00.pdf
- https://www.ibm.com/developerworks/rational/library/content/legacy/parttwo/1000/0669/0669_Rosenberg_Ch03.pdf
- https://www.ibm.com/developerworks/rational/library/content/RationalEdge/may02/m_chapter4_jr.pdf
- https://www.ibm.com/developerworks/rational/library/content/legacy/parttwo/1000/0670/0670_Schneider_Ch07.pdf
- https://www.ibm.com/support/knowledgecenter/en/SSWSR9_11.0.0/com.ibm.pim.dev.doc/pim_ref_usecasetemp.html

UML 2.0
- http://agilemodeling.com/style/useCaseDiagram.htm
