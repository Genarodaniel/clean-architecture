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



##### USE CASES ##################

- Use cases in CA means the intention of a component from our software
- Each action is a intention, each intention is a use case. 
- To clarify each behavior of the software 
- Details should not impact in the business rules
- The art of work with clean architecture is to postergate the max the details like: "Which Database we should use? Which queue system ? Grpc? Rest? "



##### USE CASES - SRP (Single responsibility principle) ##################

- We tend to "reuse" use cases because they are similar.
- Ex: Update vs insert. They both verify if the register exist, and persists the data. But, are use cases differents, whey ?
- SRP (Single Responsibility Principle) => Changes for different reasons
- Resist the will to change code, each use case has his own responsibilit 


##### Real repetition x accidentally 

- It's not a problem to copy some code that have different intentions, you may repeat yourself in a query searching for a register before insert or update. 


###### Use cases tell a story ########

- Use case is the end of the automatization which is the software
- It rules the order things going to happen like: first  receive params, validate x, after validate y, get credit score, validate how much it is and then make a decision. 
- ValidateName, validateAddress is going to be in the business rules
- Entity is where we store the business rules.


###### architecture limit #####

- Any tool that doesn't impact in the business rules should be in a architectural limit differente, like the type of db, the queue system, the front end technology. 
- Entity are business rules. 


###### Input x Output #########

- At the end of the day everything summarizes as an output. 
- Ex: Create an order (order data = input)
- Order created (response order data)
- Simplify your logic only thinking in input x output.
##### !!!!!!!!!!! IMPORTANT !!!!!!!!!!!!!!! ##########

- 1 - When you receive a data you receive it in the External interfaces such as (GraphQL, Rest, GRPC, CLI).
- 2 - The Controller identify where this came from and do the validations and then send it to the use cases. 
- 3 - The use case have the Responsibility to do the intention of the software, and it knows which business rules this call should access. 
- 4 - The use case send this input to the business rules and receive an output, use case will continue the flow and send the output for the controlle which send a response to the external interfaces.

Ex 2 : 

- The present transforms the output in to the correct format depending of which external interface called it. 

- 1 - The flow is: 
- Controller -> Use case input Port -> Use case interactor -> Presenter -> Use case Output Port and then rewind backwards. 


#### DTO - (Data Transfer Object) #######

- To travel data among the architectural limits. 
- This is a object without behavior, only have data, no rules.
- There's a DTO for output and another for input, because they are different.

Flow: 

- API -> CONTROLLER -> USE CASE -> ENTITY. 
- Controller creates a DTO with the request data and send it to the use case 
- Use case execute it's own flow, get the result, create a DTO for the output and send a response to the controller again. 



#### Presenters ###### 

- Transformation objects
- Format the DTO output in the right format to deliver the response.
- TR: A system may have many formats of delivery ex: XML, JSON, Protobuf, GraphQL, CLI, etc.


Ex: 

    Input = new CategoryInputDTO("name");
    Output = CreateCategoryUseCase(input);
    jsonResult = CategoryPresenter(output).toJson();
    xmlResult = CategoryPresenter(output).toXml();



###### ####### Entity ##############

- Entity in CA -> Layer
- Entity in DDD - Represents something unique in the application
- Clean architecture defines entity as the business rules layer. 
- Them are applied in any situation. 
- There's no explicit definition about how to create entities. 
- Usually we use the DDD techniques. 
- Entities in CA = Agregates + domain services. 
