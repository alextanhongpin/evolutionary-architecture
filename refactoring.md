Refactoring is essential to keep a healthy codebase. It's akin to visiting the dentist/doctor once in a while to ensure things are doing well (but you are the doctor, so if you say everything is okay, then it's okay).


There are some useful ways to refactoring, but generally it's about recognizing the patterns, and extracting them, making them more modular:


- recognizing patterns, extracting them: https://refactoring.guru/
- reducing cyclomatic complexity: https://ruwandotnet.wordpress.com/2012/03/13/59/ 
- using domain driven design


Note that there are other means of refactoring that is not taken into account here, such as those at the architectural level (microservice vs monolith, even sourcing etc). Always see what can be done at the code level first before deciding on such large refactoring.
