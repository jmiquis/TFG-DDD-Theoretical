layout: page
title: "command-handling"
permalink: /command-handling

# Command bus 🪖 🚌
## layer : domain 🔴 / infrastructure 🟢

As in event bus, the command bus is the channel which transports the commands from the infrastructure layer to the command handler.

In this case, the command handler interface has one method called dispatch and it accept commands as parameter. Notice that in this case I used the axon command gateway instead axon command bus and the method dispatch is `sendAndWait` which waits until the command finishes the validation processes and then is published.

```
package com.shared.domain;

public interface CommandBus {

	void dispatch(Command command);
	
}
```
Then it comes the implementation in the infrastructure layer: 
 
 ```
 package com.shared.infrastructure;

import org.axonframework.commandhandling.GenericCommandMessage;
import org.axonframework.commandhandling.gateway.CommandGateway;
import org.springframework.stereotype.Service;

import com.shared.domain.Command;
import com.shared.domain.CommandBus;

@Service
public class CommandBusAxonImplementation implements CommandBus{
  //DEPENDENCY INJECTION
	private CommandGateway commandGateWay;
	
	public CommandBusAxonImplementation(CommandGateway commandGateWay) {
		this.commandGateWay=commandGateWay;
	}
	
  //USES OUR CUSTOM COMMAND CLASS AS A AXON PREDEFINED COMMAND
	@Override
	public void dispatch(Command command) { 
		this.commandGateWay.sendAndWait(GenericCommandMessage.asCommandMessage(command));
	}
}
 
 ```




 
 # command handler 🪖 ✋
 
 - First in the controller: 
 
 
 ```
 @RestController
public class AssignNewIncidencePutController {

	//THE COMMAND BUS INSTANCE IS INJECTED
	CommandBus commandBus;
	
	public AssignNewIncidencePutController(CommandBus commandBus) {
		this.commandBus=commandBus;
	}
	
	@PutMapping("/assign-incidences/{id}")
	public ResponseEntity<HashMap<String, String>> assign(@PathVariable String id, @RequestBody HttpAssignRequest request) {
		
		try {
			AssignIncidenceCommand command = new AssignIncidenceCommand(id, request.getWorkerId());
			commandBus.dispatch(command);
		
		} catch (Exception e) {
			return ResponseEntity.badRequest().body(new HashMap<String,String>(){{
				put("exception_message",e.getMessage());
			}});
		}
		return ResponseEntity.ok().body(new HashMap<String,String>(){{	
			put("assigned_to_worker",request.getWorkerId());
			put("incidence",id);
		}});
	}
	
}
 ```
 - Once the command bus dispatches the command the method annotated with @CommandHandler does its thing:
 
 ```
 @CommandHandler
	public void createIncidence (CreateIncidenceCommand command) { //COMMAND LISTENED AS PARAMETER
		
		List<String> allWorkers   = wokerRepo.getAllWorkers();
		if(!allWorkers.contains(command.getCreator()))
			throw new IllegalArgumentException("The id "+command.getCreator()+" doesn't match with any of our registered workers");
		
		final IncidenceId id                        = new IncidenceId(command.getId());
		final WorkerId creator                      = new WorkerId(command.getCreator());
		final IncidenceCreationTimeStamp createdOn  = new IncidenceCreationTimeStamp(Constants.TIMESTAMPS_FORMAT.format(new Date()));;
		final IncidenceShortDescription description = new IncidenceShortDescription(command.getDescription());
		
		final Incidence incidence = Incidence.create(id,creator,createdOn,description);
		incideceRepo.save(incidence);
		eventBus.publish(incidence.pullDomainEvents());
		
	}
	
 ```



---
## [index](https://jmiquis.github.io/TFG-DDD-Theoretical/) 
---
