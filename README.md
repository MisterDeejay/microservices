# Microservices
The following is a short write up on the pros and cons of taking a microservice approach to web development. It may not be exhaustive, but it an attempt to give someone familiar with the approach a solid base of understanding and potential argument for why moving to a microservice architecture might be the right decision and what challenges one might face. The second part of the write up is an attempt to give someone an overview of the steps to take when migrating from a monolith application to a microservice architecture.

* [Microservice Pros](./README.md#pros)
* [Microservice Cons](./README.md#cons)
* [Moving from a Monolith to Microservices: The Guide](./README.md#steps)

## Pros
The use of microservices are one of the more recent advancements seen in web development architecture. The design pattern was developed as a response to the proliferation of monolith applications seen at many companies. A monolith application is a piece of software that contains all the code connected to the business, with one database and many subsystems holding a number of various responsibilities. By definition, the microservice design pattern results in a collection of loosely coupled services, with the division between each service being based on functionality.

Traditionally, functionality between each microservice is exposed through a REST API and messages are exchanged through HTTP and JSON. The collection of microservices results in the entire application, and ideally the individual microservices can be abstracted away when referring to the application as a whole. One benefit of this approach is that each service can now have not just its own data source, but the entire tech stack can be customized completely separating different domains and giving you the flexibility to use the right set of tools for each microservice.  This promotes weak coupling between subsystems and can reduce dependencies.

Another advantage of such an approach is an increase in modularization and independence of separate subsystems. All services can be independently deployed giving DevOps a more granular approach to application support. Different parts of the app can sit on different types of servers and increased control is given to scale only the "high use" services. Under a monolith design pattern, the entire application has to be scaled to support the subsystem that is most used. Ultimately, this more granular approach to scalability can result in cost savings for the business.

Each service can now be tested, built and deployed on its own. This can result in easier and more frequent deployments now that the test suites are smaller. The build/release workflow becomes faster and feedback from the CI server after commits can reach the team in a shorter amount of time. Now that the release of one part of the app is no longer blocked by another, the engineering department can break into smaller teams that can work in parallel increasing overall productivity. Teams working in parallel on independent services means less team dependency which again results in faster development.

With the transitiion to microservices, continuous delivery becomes easier and deployment risk should go down now that releases are tied to smaller services (i.e. a failed deployment should not result in the entire application failing). Higher test coverage should be more easily achieved as a result of the independence of services and the smaller number of coarsely-grained REST calls rather than a large number of in-process method calls. If the services are built in a resilient manner, with ways to handle network failure and/or dropped messages, the application should have a smaller downtime. Finally, the re-usability of difference services is now possible, which should keeping the application DRY.

## Cons
Of course, all design pattern are not without their downsides, and most of the problems associated with the microservice design pattern center around dealing with distributed systems. That, of course, includes increased effort around operations, deployment and monitoring now that you have multiple services that require their own separate deployment unit that has to tested, released and monitored all on their own. The culminates in an increase in configuration management. Another major point of concern is making sure to keep service dependencies compatible after updates, something you do not have to worry about when all the code is contained in a single monolithic application. 

Separate services also means more points of failure and potentially unsafe communication through HTTP rather than through internal API calls. With communication over a network comes the possibility of network failure and now the software has to be built with this in mind and must be resilient in order to handle network loss or failure.

In addition to reduced transaction safety over networks, you also have to worry about unsafe distributed communication as handling callbacks becomes more difficult. What happens when a transaction rolls back, but the downstream microservice has already been notified? Now that messages are being sent asynchronously over a network, your code must be built with that in mind. Microservice architecture also suffers from a performance hit to the the network overhead and HTTP (de)serialization. Data redundancy and data synchronization practices like caching and sharding, however, can help to minimize the performance loss. Refactoring can also be more difficult, particularly when moving code between microservices. 

## Moving from a Monolith to Microservices
### Steps
When splitting a monolithic application, the number one driver should be decoupling capabilities to release them independently. The first step in this process is to identify the code for separation. You want to start with the edge services that are the most decoupled. This identification can be done through static analysis tools, like RubyCritic, to find naturally isolated code. Other good candidates for service separation are:
1) subsystems that require the fewest number of changes to client facing applications,
2) subsystems that don't need a data source, and
3) subsystems that are candidates for reusability and extraction versus rewrite and retire.

Starting this way is advantageous for a few reasons; by starting with the subsystems that are most decoupled, you are able to quickly test and optimize for your delivery validation. It also gives less experienced team members some experience with relatively easy subsystems by building out the smallest piece required to deliver an independently deployable secure service that exposes a self-serviceable API.

Before the creation of the new service can actually start, it is important to first set up the deployment environment, the CI pipeline and the debugging/montioring/logging architecture. That way, there is no lag time between service completion and the go-live date. During this process, it's also important to keep in mind that the ultimate goal is to always move in the direction of removing dependencies from the monolithic application. In cases where avoiding service dependencies means adding calls back to the monolith, it becomes necessary to expose a new API layer.

Once these initial, less difficult subsystems have been successfully separated to their own services and the approach has been validated, the more difficult task of moving out services that have user facing components or that need a separate data source can be attempted. All services should be set up as facade services to provide developer friendly APIs. All shared data between services should continue to remain in one schema/storage system. Overall, the goal should be to move from the macro to the micro and to migrate in atomic evolution steps. In other words, begin with an analysis of the entire app, come up with a plan of attack and then begin moving the smallest piece first. Make sure that every time a decision is made to move some code into its own separate service, it is an all or nothing move.
