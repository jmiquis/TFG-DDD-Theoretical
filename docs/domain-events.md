layout: page
title: "domain-events"
permalink: /domain-events

----

# Domain events 🥳
## layer : application 🟡

When something happens in our app, an event should be released. If the the event is important for somebody, this somebody would be the event listener.
In terms of event sourcing, I decided to delegate in Axon framework.
It's a framework that manages every stage of ES, and CQRS, and its use is extended over the spring boot community.
It works by annotations and there is a complete official documentation @ [axon official site](https://www.axoniq.io/)

The main reasons to use event source are:
- app components decoupling
- monitorize what is happening in our app

Domain events are DAO classes ,always named in past tense ,that holds the necessary data to spread the new of an action ocurred.
Basic domain event info is:
- when event was ocurred
- the aggregate that triggered the event
- some other stuff such as event name or event id

```
package com.incidences.application.assign;

import java.io.Serializable;
import java.util.Date;
import java.util.HashMap;
import org.axonframework.modelling.command.AggregateIdentifier;
import org.axonframework.modelling.command.TargetAggregateIdentifier;
import com.shared.domain.Constants;
import com.shared.domain.DomainEvent;
import lombok.AllArgsConstructor;
import lombok.Getter;

@AllArgsConstructor //lombok constructor annotation
@Getter // lombog getter annotation

public class IncidenceWasAssigned extends DomainEvent {

	@TargetAggregateIdentifier // axon annoptation in ordder to identify the event id
	private final String incidenceId;
	private final String ocurredOn;
	private final String assignee;
	private final String eventId;
	private final String description;
	
	public IncidenceWasAssigned(
			String incidenceId,
			String assignee,
			String description
			) {
		this.incidenceId = incidenceId;
		this.assignee    = assignee;
		this.ocurredOn   = Constants.TIMESTAMPS_FORMAT.format(new Date());
		this.eventId     = super.getUUiD();	
		this.description = description;
	}
  
  // rest of the class
```


When the domain event is created in the application layer, we create the event listener method, which will be waiting for the event be released.
It's important to say that the event listener should be in the application layer. 

Event listener are services.


```
package com.workers.application.addOneIncidence;

import org.axonframework.eventhandling.EventHandler;
import org.springframework.stereotype.Service;
import com.incidences.application.assign.IncidenceWasAssigned;
import com.workers.domain.Worker;
import com.workers.domain.WorkerId;
import com.workers.domain.WorkerRepository;

@Service //HIBERNATE ANNOTATION.NEEDED
public class IncidenceNumberIncrementer {
	
	WorkerRepository repository;
	
	public IncidenceNumberIncrementer(WorkerRepository repository) {
		this.repository=repository;
	}
	
	
	@EventHandler //AXON ANNOTATION
	private void increment(IncidenceWasAssigned event) { //THE LISTENED EVENT IS THE METHOD PARAMETER
		
		WorkerId id   = new WorkerId(event.getAssignee());
		Worker worker = repository.searchById(id);
		
		if(worker==null) throw new IllegalArgumentException("invalid worker Id");
		
		worker.incrementNumberOfTasks();	
		repository.save(worker);
	}
	
}
		
	}
```
As we can read in the code above, the use case just uses the information that brings the domain event to do its thing.
Any event can have n listeners.

Now we are going to see how the event is created into the aggregate root class.

```
public IncidenceAssignation assignIncidenceToWorker(WorkerId assignee) {
		IncidenceAssignation assignation = new IncidenceAssignation(this.id,assignee); 
		this.addAsignee(assignation);
    // EVENT IS CREATED HERE
		IncidenceWasAssigned assignedIncidence = new IncidenceWasAssigned(id.getIncidenceId().toString(),assignee.getWorkerId(),description.description);
		return assignation;
	}
```

### ❗ Axon provides us ways of store our events but I didn'd get to deep into it. It creates some sql tables by itself but you can create your own event store class.


## some really good content about domain events
- [domain handling axon official](https://docs.axoniq.io/reference-guide/v/4.0/implementing-domain-logic/event-handling/dispatching-events)
- [![axon event storage](http://img.youtube.com/vi/zUSWsJteRfw/0.jpg)](http://www.youtube.com/watch?v=zUSWsJteRfw "axon event storage")

---
## [index](https://jmiquis.github.io/TFG-DDD-Theoretical/) 
---
