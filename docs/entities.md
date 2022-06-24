layout: page
title: "Entities"
permalink: /entities
---
# Entities 👻
---


The most common example use to explain a DDD entity is _person_. A person  is well differenced to another beacuse of a unique Id number. We have seen examples like this hundred of times.

In terms of code ,an entity no more than a classic class with its unique Id, its own methods and attributes, but remember that **these attributes are value objects** 

```

@Entity(name="incidence") //JAVAX PERSISTANCE ANNOTATION
@org.axonframework.modelling.command.AggregateRoot //AXON FRAMEWORK ANNOTATION
public class Incidence extends AggregateRoot{
	
	
	@EmbeddedId //VALUE OBJECT
	@AttributeOverride(name="value", column=@Column(name="incidence_id"))
	@TargetAggregateIdentifier
	IncidenceId id;
	
	@Embedded //VALUE OBJECT
	@AttributeOverride(name="value", column=@Column(name="creator"))
	WorkerId creator;
	
	
	@Transient
	ArrayList <IncidenceAssignation> asignees;
		
	@Embedded //VALUE OBJECT
	IncidenceCreationTimeStamp createdOn;
	
	@Embedded //VALUE OBJECT
	IncidenceShortDescription description;
	
	@Embedded //VALUE OBJECT
	IncidenceStatus status;
	

	private Incidence() {}
	
  //CONSTRUCTOR
	private Incidence(
			IncidenceId id, 
			WorkerId creator, 
			IncidenceCreationTimeStamp createdOn,
			IncidenceShortDescription description) {
		this.id          = id;
		this.creator     = creator;
		this.createdOn   = createdOn;
		this.description = description;
		this.asignees    = new ArrayList<IncidenceAssignation>();
		this.status      = new IncidenceStatus("OPEN");
	}

```
