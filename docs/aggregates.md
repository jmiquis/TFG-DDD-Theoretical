layout: page
title: "aggregates"
permalink: /aggregates

# Aggregates 👨‍👨‍👧

## layer domain 🔴

The aggregate concept was one the most 🤯 in all DDD, at least for me. 
I have read many aggregate definitions but if I have to choose one of them, I will choose this : "Its a group of entities wherein the consistency must be transactional".
A common example is order/order_line. 
In a purchases cart you cannot afford add some items and let other purshased items out of the order. Your clients will complain and your E-shop will run out of business soon.
This can be called "aggregate invariant"
An invariant is a business requirement which must be accomplished always, so you have to build an aggregate in roder to maintain it transactional.
There can aggregates from one and only entity up to all the app aggregates (the big aggregate) but I have seen only one or two entities aggregates examples.

Carlos Buenosvinos explained [here](https://www.youtube.com/watch?v=lIVVX-RnGps) that its not necessary to create aggregates just because. Instead, eventual consistency is ok, when for example there is no a business rule that force us to show a state change inmediately.
For instance, he talks about atrapalo which is a hotel review site, where there was not necessary to add one to the number of a hotel total reviews instantly, so the decision was keep the eventual consistency in that case.

### When we have an aggregate, we have to choose only one class in order to be the communication port: the aggregate root.
Usually the class which hosts a larger number of inner classes is selected as root.
Every class into the aggregate becomes root dependant. If I want to create any child class, It has to be created by the way of the root class.

```
@Entity(name="incidence")
@org.axonframework.modelling.command.AggregateRoot
public class Incidence extends AggregateRoot{
//CONSTRUCTOR
//METHODS

  //I created this class INSIDE the root class in roder to show how root dependant it was
  @Entity(name="incidence_assignation")
    public class IncidenceAssignation{
      @EmbeddedId
      AssignationId id;

      @Embedded
      AssignationTimestamp ocurredOn;

      //The constructor is private because this way you can only instantiate the child class by the root class way 
      
      private IncidenceAssignation(IncidenceId incidenceId,WorkerId assignee) {
        this.id = new AssignationId(incidenceId,assignee);
        this.ocurredOn   = new AssignationTimestamp(Constants.TIMESTAMPS_FORMAT.format(new Date())); 
      }

      public IncidenceId getIncidenceId() {return this.id.incidenceId;}
      public WorkerId    getAssignee() {return this.id.assignee;}
    }
}
```


Now, we are going to see how the child class is instantiated

```
package com.incidences.application.assign;

@CommandHandler
	@Transactional
	public void assign(AssignIncidenceCommand command) {
		WorkerId assignee       = new WorkerId(command.getAssignee());	
		
		if(!workerReporsitory.getAllWorkers().contains(assignee.getWorkerId())) 
			throw new IllegalArgumentException("This id doesn't match any registered worker id");
		
		IncidenceId incidenceId          = new IncidenceId (command.getIncidenceId());
		Incidence incidence              = incidenceRepository.searchById(incidenceId);
		
		
		if(!incidenceRepository.allIncidencesId().contains(incidenceId.getIncidenceId())) 
			throw new IllegalArgumentException("This id doesn't match any registered incidence id");		
		
    		//Here is instantiated. Before I had instantiated the root class
		IncidenceAssignation assignation = incidence.assignIncidenceToWorker(assignee); 
		
		incidence.changeStatus("ASSIGNED");
		incidenceRepository.addAsignee(assignation);
		eventBus.publish(incidence.pullDomainEvents());
		
	}

```

## ❗ You should not be able to create a child class by itself.
---
Some great content about aggregates:
- [Aggregates in Domain Driven Design](https://medium.com/ingeniouslysimple/aggregates-in-domain-driven-design-5aab3ef9901d)
- [![Carlos Buenosvinos talk Spanish](http://img.youtube.com/vi/lIVVX-RnGps/0.jpg)](http://www.youtube.com/watch?v=lIVVX-RnGps "Domain-Driven Design: Aggregates (Spanish)")
---
## [index](https://jmiquis.github.io/TFG-DDD-Theoretical/) 
---
