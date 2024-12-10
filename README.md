# clean-architecture
This is a project to study about clean architecture and implement it in a simple order service



###### Key concepts about architecture  #############

Key concepts about architecture:


- The format software will have. 
- Division of components (Think about architecture is thiking about which components the software will have)
- Communication among components (just like in a building to move floor to floor you'll need an elevator or a staircase)
- A good architecture will help the development, deploy, operation and maintenance of the application.


"The strategy behind that facilitation is to leave as many options open as possible, for as long as possible." 


###### KEEP OPTIONS OPEN #############

KEEP OPTIONS OPEN : 


- Business rules are the real valuable thing for the software
- Details helps to support the rules
- Details shouldn't impact the business rules (when architecturing a software you should be more aware of the business rules than the technology, the rabbitMQ x SQS is detail, the software must deal with a Queue, no matter what type it is)
- Frameworks, databases, apis, shouldn't impact the business rules.


####  Remember DDD? ####################

To attack the complexity in the heart of the software. 

Business rules are the heart of the software, the pluggable things are details(sqs,mysql,graphql,rest)