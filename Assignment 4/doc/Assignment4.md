# Final Solution And Self-reflection

[TOC]



## 1.Achievement

## 2.Limitation

### 2.1. User experience: Lack of special handling mechanism for ordinary users

In our system we distinguish between ordinary users and senior users by issuing coupons or not, which means ordinary users can never get coupons unless they become senior users by some ways. This mechanism is bound to be conducive to senior users, while that keeps the senior users being absorbed in our online book store. But for the ordinary users, the book store is not that attractive. The lack of special handling mechanism for ordinary users may lead to the loss of new users who can not buy books firstly in a lower price.

### 2.2. Lower level design: A rough design of database.

During our analysis and design work, we did not design a relational database systematically but only made a rough design of database in several times. This mirrors the lack of design in data layer and model layer. Without a design of the whole database structure, we only considered the functions of each entity class, which led to some repetition and some omissions of methods among entity classes. This rough design really reduced our efﬁciency because we uniﬁed the functions and operations among different data models more than once. And we have learned from this limitation that the systematical design of the whole structure should always come ﬁrst. 

## 3.Architectual style

The basic principle we use in architectural design is layered architecture. A layered architecture is the most common architecture and is also known as an n-tier architecture. Many companies and companies have used this architecture in their projects for many years, and it has become almost a standard norm.

So in our architecture there are four layers: Presentation Layer, Services Layer, Controller Layer and Data Layer. Layering does not need to know how other layers do it. For example, the controller layer does not need to know how the data layer queries the database. On the contrary, when the controller layer calls the specific method of the data layer, it only needs to pay attention to whether part of the data or all the data is needed. This is what we call the separation of concerns, which is a very powerful feature to let each layer is responsible for its responsibilities.

## 4.Potential Improvements

In the third assignment, we designed a primary Architecture and we made some improvement of it in this assignment.

### 4.1 Design asynchronous version APIs on the Presentation Layer

Introduce asynchronous mechanism on the web frontend. The updated web frontend uses the asynchronous design version APIs, such as AJAX and jQuery. 

For example, the working flow for a specific procedure: 

1) At first, the customer wants to get contract list from the customer frontend. 

2) Then the customerFrontend will send a GET or POST request to the contractController. 

3) The controller will get the contract list from the database of contract. 

4) The controller returns the serialize of the contract list to the CustomerFrontend. 

5) The CustomerFrontend updates only the part which needs to update. 

### 4.2 Update the Service Layer by splitting it into two parts

We know that the Service Layer provides unified API interfaces of all
functionalities for both web clients and mobile clients.

The updated API is split into two parts: the web APIs(containing Mapping
System and Payment System) and the real-time APIs(containing Dialog
Controller and Order info Interface).

The updated layer is shown as below:

![Services Layer.png](https://upload-images.jianshu.io/upload_images/12014150-c18b46665fc91f5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

The web APIs are provided in the form of JSON services based on HTTP/HTTPS. Web API interface serve functionalities that fit the request-and-response model. Most of the functionalities are provided by this kind of API.

The real-time APIs are provided in the form of JSON services based on
WebSocket protocol, which allows modern web browsers (such as IE10+,
Chrome and Firefox) to access it using HTML5 WebSocket feature.Functionalities that requires “server side push” are provided by this kind of API.

One typical example is the package real-time tracking functionality.

### 4.3 Add a WAF at the outside boundary of the Infrastructure Layer 

Considering the safety issues, WAF (Web Application Firewall) can be added at the most outside boundary of the Infrastructure Layer. 

The WAF middleware controls all data access to API. 

The WAF should at least provide limiting rating mechanism and application-layer access control (for example, IP blocking) functionality, aims to reduce security ricks from large scale brute-force / dictionary based enumerations. CAPTCHAs and access-blockings are both kinds of rate limiting mechanism. 

### 4.4 Add a model layer to get data more efficiently

In order to search data more efficiently, we add a model layer between Controller layer and Data layer. 

Models are divided according to classification in subsystems, and are in correspondence with classes in database. We design this layer to let upper layer get access to the database more clearly and easily.

The model layer is shown as below:

![Model Layer.png](https://upload-images.jianshu.io/upload_images/12014150-be85ab0876f1c0f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5.Design patterns

To achieve our web platform, we mainly use Django and Vue.js, so in this part we will mainly talk about the design patterns of this two frameworks.

As for Django, it follows the model-view-template(MVT) pattern, which can be treated as a special form of MVC. It consists if an object-relational mapper(ORM) that mediates between data models, and the workflow if MVT pattern is as follows:

1)Web Server(middleware) receives a HTTP request

2)URL dispatcher dispatches the request to specific views

3)View functions call corresponding models to access data and invoke the corresponding template to present the page to user

4)View functions finishes its task and return the HTTP response to middleware

5)Web Server(middleware) sends the response back to client

As for Vue.js, it is a progressive framework for building user interfaces. Unlike other large frameworks, Vue is designed from the bottom up. The core library of Vue focuses only on the view layer, making it easy to get started and easy to integrate with third-party libraries or existing projects. 

[此处可贴mvt模型的图]

## 6.Critical decisions

6.1 Data Storage

To choose a suitable storage method for our project, we first analyze the data types that we may encounter: data that is highly relational and has great demand of ACID, data that is less relational and data that is highly performance sensitive like cache and sessions. After discussion, we decide to use MySQL, MongoDB and Redis together to store the three kinds of data above.

For MongoDB, the system uses ODM(Object Document Mapping)frameworks to map objects to MongoDB queries and for MySQL, it uses ORM(Object Relational Mapping)frameworks to map objects to SQL queries.

Both the two methods and database management systems are indispensable
and can make the queries and update more efficient.

[此处可贴存储结构的图]

6.2 Senior User

When trying to define all the classes that contained in our project, we create a class called senior user derived from normal user. This class has the privilege to possess coupons and can interactive with the manager.

As we are a private bookstore, we do not need to create various kinds of coupons and create a special kind of user can help reduce the data that needs to be stored and transffered.
[此处可贴和高级用户及优惠券相关的图]

## 7.Open Issues

### Robustness

Improve robustness of our design model includes
architecture design, program coding.

Architecture design is a very large range, generally we are not particularly
real-time business to exchange time for performance, space for performance and
other programs. For example, we can store some original data about books first in the database, until the middle of the night when the pressure is small, and then we deal with them further.

For inquiries, with the aim to read more and write less, we generally take CQRS (Command Query Responsibility Segregation) program, as reading may also use the search engine technology and we do not use distributed
transactions, but take the compensation mechanism in order to coordinate multiple system changes.

As for program codes, which is the last remaining thing to be seen, it is necessary  for programmers to write robust codes. It can be partly measured by the judgement of the null value of string. Moreover, timely de-allocating the objects, avoiding writing dead loops to implement sleep, avoiding writing a possible death cycle. When writing code, programs can fully utilize some Program Static Analysis tools to find bugs and improve quality of codes, such as SONAR, PMD and so on.

### Maintainability & Expandability 

Take the processing of online payment as an example, our system provides an interface and concrete class. For various kinds of payment platforms, the system encapsulates the payment subsystem. And whenever we want to change the way we pay, we can only extend the functionality of this subsystem. 

Thus, we can extend the other functions according to the original subsystem just in the similiar way. 

#### Form a Book Dispatching Schedual System

As the bookstore grows bigger, it may need more and more methods to fetch tht books and send them. If there are certain somebody to handle the dispatching job, then it is really unnecessary and a waste of resources.

Form a dispatching schedual system can not only save the cost and resources but provide the 24-hours analysis for the current stock and orders, by which bookstore can know whether it need to buy books from the publishers or send books to customers in time. We may also add some machine learning algorithms to make the system smarter and more efficient.

## 8.Development Plan

Because it  need hard work to build the front end and the user interface, we plan to have two of our group members to learn some knowledge about the implement of the front end and the user interface and after that they will implement corresponding subsystems through calling required and provided interfaces. The learning and implement time will take one and a half months.

For the design of database and back end, it is done by three of our group members. Two of them may spend one week to design ER diagrams and turn them into tables. We are going to dispatch the left person to build and manage database of MYSQL, Redis and MongoDB. As we think it will not be too hard, so we plan to finish this in two weeks.

Then, for the sever connection and the connection between back end and front end, as it is very important, this part is distributed to the back end team and front end team together. This part of work is to deal with requests and return information, so the back end need to coordinate with the front end. As we may encounter some problems, two months is necessarily needed.

 Finally, after the above work has been done, we need to test our system, debug and complete deficiencies that have been found during the test process. This part may take up one month or more because the whole group has to do that together and be responsibility for every subsystem.

## 9.Self-reflection

### Zhe Zhang(张喆) [Team Leader]

In this semester, as the team leader of Group 3, I have witness the whole System Analysis & Design for our online bookstore —— YouMu(UM) bookstore.

At the beginning of our design, we don't know how to start the whole things, which means you have got an assignment to build a system like Dangdang or other online bookstore, you don't even know where to start(Interfaces?Function?Architecture?or other thing)

At the first class this semester, our professor Cao told us what is an ideal design, and what's the development mode of our common design and also we get a global view of "What is UML and what is Object-Oriented Analysis and Design".

And when we do our Assignment 1, we start with with a brainstorm, everyone shared his standpoints and we list all the thing there. Then we discuss all the items step by step in detail, and we confirm the users, use cases, main function of our bookstore, and more things. Nonetheless, on the first presentation, we found ourselves forgot something more personal, which mean we regard the origin task as a project and homework we must finish, not a real system we build our self.

So, we polish the first document into fresh version, which add our own idea for our online bookstore: We name the bookstore as YouMu bookstore, which the letter in Chinese is UM, it represent the content we take in class —— UML. And since the second presentation, we create our own PPT template, which reflect our idea of the bookstore —— Chinoriserie.

We have tried so many tools to draw the UML chart more efficient and beautiful, and meanwhile, we learned a lot in use these software and website.

As for the references, we searched quite a lot in the website and library. Because at the beginning of the design, we cannot hold a clear mind, so we read some article which introduce how to design analysis and design a system, and to be honest we also take example by some blogs and article, but I swear we done all the thing by ourselves and we just used them as references.

As the team leader, I have learnt something more this semester  —— teamwork. You should master the whole thing we are supposed to do prior to your team members, and you should organize the short meeting nearly three times a weak, I tried to allocate the task in equal, and I do think what I done well was that I allocated different work for each one in each assignment. So I think all of my team member have learned many concept in UML, not only one part. And we changed 4 individuals for each presentations, this will also help them grasp more skill.

In the end of this course, I want to say that both the Professor Cao and Professor Liu did an extraordinary job, which made me enjoy this course. One thing is special in my mind, in the first presentation, Professor Liu waited until 10:30pm to listen all the group and she even stop during the 3 hours, and the essential thing was that she gave an evaluation for each group, so we can find our deficiency and we can complete ourselves. I must say THANKS to our teachers, You are our role model.

I hope we will take our analysis and design into real work, and we can finish our own YouMu online bookstore one day!

Thanks Professor Cao and Professor Liu again, and I want to say to my team members that you have worked hard, thanks everyone!

### Siteng Ma(马思腾)

From my perspective, UML is useful but not easy to be taken advantage. The online bookstore conception gets mature by the discussion and cooperation of us five. Except for the knowledge taught during the class, there are a variety of principles for us to learn by doing and searching in the internet. By designing the subsystem and drawing plenty of diagrams, I realize the importance of UML after implementing the whole system. The vary steps to refine the whole project are miserable but essential. We have overturned the models several times and replaced with a better one.  So that we have finally established a UML model which is not that bad. We five boys consolidate firmly and learn much by doing these four assignments. All in all, I benefit a lot through the team work in this semester.

### Gefei Wang(王舸飞)

During the whole project, although I mainly focus on prototyping, presentation and some activity diagrams, I also learned a lot while communicating and discussing with all my team members. Choosing which architecture and framework to use requires us to have a clear understanding about the advantages and disadvantages of all the methods.
As a sophomore that dose not have so many experiences, I searched a lot information on the Internet and referred many books that recommended by our professor. After getting my own part done, we  also shared our knowledge with other team members to make sure that we are always keeping in touch.

As for the limitations, I think we still lack of real experiences, which means we did not have many choices when implementation. Actually, as the user's requirements will also change, it is acceptable that we will have to make changes to our previous architecture, and that is exactly the way a good product is made.

### Le Yang(杨乐)

During the process of developing our project, I was mainly responsible for design the use case model, analysis model. At first I was totally in the dark about what to do, but with the direction of our professor and help from my team members, I improved myself a lot and finish my job. The main shortcoming of me is that I was too hurried and sometimes neglected the feelings of other team members, so I need to overcome this in the future because the feelings of other team members is very important for good team work. Here, I want to thank my team members and my director, for they made me do better than I could do.

## 10.Course Reflection

### 10.1 Teamwork

For the teamwork, actually it shouldn't be neglected, because it is so important for our efficiency. At First, we were completely in the dark about project and so worried about it. But it is the group discussions from time to time that help us to make process. We first have a brainstorm about the project, and it truly help us to find the direction. Then We made plans and divided our team into small groups to finish the job. Finally, as the professor told us, we do this big project step by step in an iterative way.  Therefore, just as the old saying goes, "where there's a will, there's a way." We learned new things and make process step by step and now we are proud of our final project. We can say that our efforts are worth the time.

### 10.2 UML Developing Tool

For the UML developing tool, our group members are using different tools at first. Three members use StarUML and two members use processon. These two tools have their advantages and disadvantages. StarUML is more convenient and processon is more beautiful. For our third assignment, we find a new tool named draw.io. We use it to draw our architecture system diagram and class diagram, because its style is good and it is easy to use. But our use case diagram are drawed with StarUML, because we can simply drag diagrams into the diagram and it can generate global diagram automatically by using StarUML.

### 10.3 Presentation

There are totally 4 presentations for all the groups in this team. We can always make full use of our presentation time because we are Group 3. Because we do not to be normal, in addition to the our assignments, we present what makes us outstanding every time. Most importantly, we try our best to make our work conciseness and correctness. What's more, we organize our slides logically and pleasant to watch. At first our presentations are in Chinese but then we use English to present our idea because our class is in English.

### 10.4 Materials  

Our professors direct us in our class, but we still need to searching for lots of materials for reference. In addition to our textbook, we use Google and our library's site on the Internet to look for materials, which is truly helpful. Now, though we are still not very professional, we have learned a lot things about object-oriented analysis and design.

## 11.Contributions of Team Members