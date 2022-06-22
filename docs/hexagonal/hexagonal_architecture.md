## Hexagonal Architecture 💠
---

The hexagonal architecture is by far the most used architecture in terms of DDD.
It is a layered architecture created by Allistair Cockburn in 2005 and it belongs to the _clean architecture group_.
In order to create a more maintenable and replaceable code, exists a dependency rule, which only allows one layer _to know_
about itself and the immediately inner layer.

![architecture diagram](https://github.com/jmiquis/TFG-Theoretical/blob/master/docs/images/500px-Hexagonal_Architecture.svg.png)

- As we can see, the external layer (infrastructure) hosts the relationships within the app's external world.
The most common content in this layer are the domain layer implementations and the references to the persistence system, email agent, cloud service...

- In the application layer we usually found use cases or actions that our app is able to make. Here are also located one of any DDD app main elements: the domain events, which basically spread the notification of siome ocurred event through the different parts of our achitecture.

- At last, domain layer is the richiest in terms of information and concepts. It holds the business rules which influences the entire app. The most common content here are interfaces and abstract classes, implemented later in the infrastructure layer.
