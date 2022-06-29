layout: page
title: "event-bus"
permalink: /event-bus

# Event-bus 🚎
## layer : domain 🔴 / infrastructure 🟢

The event bus is the channel by which events are communicated.
First, as we do in repositories, we define an interface with the methods we think are needed to create an event bus :

```
package com.shared.domain;

	import java.util.List;

	public interface EventBus {
	    void publish(final List<DomainEvent> events); 
	}
```

Then, in the infrastructure layer we implement it: 

```
package com.shared.infrastructure;

import java.util.List;
import org.axonframework.eventhandling.GenericEventMessage;
import org.springframework.stereotype.Service;
import com.shared.domain.DomainEvent;
import com.shared.domain.EventBus;


@Service
public class BusAxonImplementation implements EventBus{

  //DEPENDENCY INJECTION 
	private final org.axonframework.eventhandling.EventBus eventBus;
	
	public BusAxonImplementation(org.axonframework.eventhandling.EventBus eventBus) {
		this.eventBus=eventBus;
	}
	
	
	
	@Override
	public void publish(List<DomainEvent> events) {
		events.forEach(this::publish);
	}

	//THIS METHOD PUBLISHES OUR CUSTOM DOMAIN EVENTS AS IF THEY WERE AXON EVENTS
	public void publish(final DomainEvent event) {
		this.eventBus.publish(GenericEventMessage.asEventMessage(event));
	}
}

```


---
## [index](https://jmiquis.github.io/TFG-DDD-Theoretical/) 
---
