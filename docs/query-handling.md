layout: page
title: "query-handling"
permalink: /query-handling

# Query bus ❓ 🚐

This one was the trickiest part to put on code.
The Axon query bus wasn't my cup of tea so I decided to create a custom one using the axon eventBus but forcing it to have one and only one queryHandler lstener by using the java generics.

```
package com.shared.domain;

import org.springframework.stereotype.Service;

@Service
public interface QueryBus <T extends QueryHandler> {
	public <R extends Response>R ask(Query query);
}
```

Then the implementation in the infrastructure layer:

```
package com.shared.infrastructure;

import org.axonframework.eventhandling.EventBus;
import org.axonframework.eventhandling.GenericEventMessage;
import org.springframework.stereotype.Service;
import com.shared.domain.Query;
import com.shared.domain.QueryBus;
import com.shared.domain.QueryHandler;
import com.shared.domain.Response;

@Service
public class QueryBusImplementation <T extends QueryHandler>implements QueryBus {

	private EventBus queryBus;
	private T handler;
	
	public QueryBusImplementation(EventBus queryBus,T handler) {
		this.queryBus = queryBus;
		this.handler  = handler;
	}
	
  @override
	public Response ask(Query query) {
		this.queryBus.publish(GenericEventMessage.asEventMessage(query));
		return this.handler.handle(query);
	}
	
}
```

# Query handling ❓ ✋

The controller, which is always a get in queries:
```
@RestController
public class IncidencesGetOneIncidenceController {

  //THE QUERY BUS IS FORCED TO DECLARE INE FINDER WHEN INJECTED. THE RELATION IS 1:1
	QueryBus<IncidenceFinder> queryBus;

	public IncidencesGetOneIncidenceController(QueryBus<IncidenceFinder>  queryBus) {
		this.queryBus = queryBus;
	}
	
	@GetMapping(value="/incidences/{id}")
	public ResponseEntity<HashMap<String, String>>find (@PathVariable String id) {
			
		try {
			FindIncidenceQuery     query    = new FindIncidenceQuery(id);	
			FoundIncidenceResponse response = queryBus.ask(query);
			
			return ResponseEntity.ok().body(new HashMap<String,String>() {{
	            put("id", response.getIncidenceId());
	            put("created_by", response.getIncidenceCreator());
	            
	            if(response.asignees.size()!=0)put("asigned_to",new Gson().toJson(response.getAsignees()));
	            
	            put("created_on",response.getIncidenceCreatedOn());
	            put("description", response.getIncidenceDescription());
	        }});
          
          //REST OF METHOD
```

At last, the query handler does its thing :

```
package com.incidences.application.find;

import org.axonframework.eventhandling.EventHandler;
import org.springframework.stereotype.Service;

import com.incidences.domain.Incidence;
import com.incidences.domain.IncidenceId;
import com.incidences.domain.IncidenceRepository;
import com.shared.domain.QueryHandler;

@Service
public class IncidenceFinder implements QueryHandler<FindIncidenceQuery, FoundIncidenceResponse>{

	IncidenceRepository repository;
	
	public IncidenceFinder(IncidenceRepository repository) {
		this.repository=repository;
	}
	
	@Override
	@EventHandler
	public FoundIncidenceResponse handle (FindIncidenceQuery request) throws IllegalArgumentException{
		
		IncidenceId id      = new IncidenceId(request.getId());
		Incidence incidence = repository.searchById(id);
		
		return new FoundIncidenceResponse(incidence);
	}

	
}
```


---
## [index](https://jmiquis.github.io/TFG-DDD-Theoretical/) 
---
