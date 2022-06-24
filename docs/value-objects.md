layout: page
title: "value objects"
permalink: /value-objects

----

# Value objects 🧱
## layer : domain 🔴

#### Value objects are indentityless classes (no id) which describe, meassure or quantify, and are identified by their attributes.
E.g: If we have 2 _color_ value objects, and it's attribute "color" is 'red', there are no 2 VO, they are the same one.

```
                WorkerPhoneExtension phoneJhon = new WorkerPhoneExtension("333");
		WorkerPhoneExtension phoneJoe  = new WorkerPhoneExtension("333");
		
		System.out.println(phoneJhon.equals(phoneJoe));
```
```
                true
``` 

#### To change a VO value, we don't really just change it. We create another one with the new value :
This makes value objects **INMUTABLES** and **side effect free**.

```
        public static WorkerEmail changeEmail(String newEmail) {
		return new WorkerEmail(newEmail);
	}
```



Value objects are the bricks, the base of our application.
In DDD we doesn't work with primitives (string, int, float) into entities. The only place where we can see a primitive is in a value object class in order to be encapsulated.
```
		@Getter
		@Entity(name="worker")
		@AggregateRoot
		public class Worker{

			@EmbeddedId //VALUE OBJECT
			@AttributeOverride(name="value", column=@Column(name="worker_id"))
			@TargetAggregateIdentifier
			WorkerId               id;

			@Embedded  //VALUE OBJECT
			WorkerCompleteName     name;

			@Embedded //VALUE OBJECT
			WorkerEmail            email;

			@Embedded //VALUE OBJECT
			WorkerPhoneExtension   phone;

			@Transient
			ArrayList<IncidenceAssignation>workerIncidenceAssignations;

```

Value objects are also auto-checked classes. This means that if a VO is instantiated, it is valid. No matter where.
This possible because of validations are in the very same class constructor as guard clauses.

```
			//VALIDATOR
			private void emailValidator(String proposedEmail) {

					int totalEmailLength       = proposedEmail.length();
					int companyDomainLength    = Constants.COMPANY_DOMAIN.length();
					String proposedEmailDomain = proposedEmail.substring((totalEmailLength-companyDomainLength),totalEmailLength);

			    // If it is not valid it just raises an exception
			    
					if (!proposedEmailDomain.equals(Constants.COMPANY_DOMAIN)) {
						throw new IllegalArgumentException("the email "+proposedEmail+(" is not a corporative valid one"));
					}
				}
  ---------------------------------
  
			  // CONSTRUCTOR
			  public WorkerEmail(String proposedEmail) {

			    //validator
					super.validator(proposedEmail); 
			    //validator
					emailValidator(proposedEmail);  
			    //instantiation
					this.WorkerEmail=proposedEmail;
				}
  ```
  

DDD purists won't like this 🔪 , but I have used annotations in this project. 
Their reason : Every annotation is an infrastructure leak which will made our domain a framework's slave. In a long term we will have to change this and it will became a problem 100 %.
My reason : I'm just arrived to DDD and I didn't want to add extra complexity to this project.

There are many valid annotations in order to create a VO:
- javax @Embeddable (my choice) , which force us to use @Embedded in the entity when referring to the VO
- lombok @Value annotation
- Java _Record_ class

```


			@Embeddable
			public class WorkerEmail extends StringValueObject{
```

When our VO is a single one (encapsulates one attribute) there is no need to add any extra annotation. Hibernate ORM will persist the VO named after the attribute.
If we have more than one attribute or simply we want to change the name in persistence system we can use the @AttributeOverride like this:

```
			@Embeddable
			public class AssignationId extends StringValueObject{
				private static final long serialVersionUID = 1L;
			  //We add extra info to the annotation
				@AttributeOverride(name="value", column=@Column(name="worker_id"))
				WorkerId assignee;
			   //We add extra info to the annotation
				@AttributeOverride(name="value", column=@Column(name="incidence_id"))
				IncidenceId incidenceId;

```


---
## [index](https://jmiquis.github.io/TFG-DDD-Theoretical/) 
---
