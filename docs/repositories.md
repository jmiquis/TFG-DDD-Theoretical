layout: page
title: "repositories"
permalink: /repositories

----

# Repositories 🏠
## layer : domain 🔴 / infrastructure 🟢

#### Repository pattern is the way that DDD has to decouple domain and infrastructure in terms of entities.

- First, in domain layer 🔴, you have to create an **INTERFACE** which does something (usually CRUD methods) but you only specify **WHAT** the repository does.
```
package com.workers.domain;

import java.util.ArrayList;
import org.springframework.stereotype.Service;
import com.incidences.domain.Incidence.IncidenceAssignation;

@Service // this annotation is important

public interface WorkerRepository {
  //just a few methods as demo
  
	public void save(Worker worker);
	public Worker searchById(WorkerId id);
	public ArrayList<String> getAllWorkers();
	public ArrayList<IncidenceAssignation>allAssignedIncidencesByWorker(WorkerId id);
	
}
```

- Later, in domain 🟢 layer, you have to instantiate that interface in ordfer to tell the app **HOW** to accomplish the task of each method. Is in this layer where SQL commands, transactions, MongoDB... may appear. Nowhere else.


```
package com.workers.infrastructure;

import java.util.ArrayList;
import java.util.List;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import javax.transaction.Transactional;
import org.springframework.stereotype.Repository;
import com.incidences.domain.IncidenceId;
import com.incidences.domain.Incidence.IncidenceAssignation;
import com.workers.domain.Worker;
import com.workers.domain.WorkerId;
import com.workers.domain.WorkerRepository;


@Repository //this annotation is needed

public class WorkerRepositoryMysqlImplementation implements WorkerRepository {
	
 
	@PersistenceContext  //this is used in order to avoid JPA and make the app less framework dependent, but I have also watched injected JPA examples.
    private EntityManager entityManager;
	
	
	@Override
	@Transactional // This annotation is necessary for save methods
	public void save(Worker worker) {
		 entityManager.persist(worker);
		 entityManager.close();
	}
  
  rest of methods

```
