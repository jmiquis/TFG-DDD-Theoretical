layout: page
title: "Strategic DDD"
permalink: /strategical

----

# Strategic DDD 🗺️

Modelling our app is a prior step to coding, but absolutely essential.
Due to the complexity of DDD, it's a must to plan our actions and define some basic concepts and put it together:

#### - Bounded context 🔴 🔵 🟤   Eric Evans defined it like "_An operational definition of where a particular model is well-defined and applicable. Typically a sub-system, or the work owned by a particular team_. ". I've read many bounded context definitions but I remember ther better one which was something like: "1 bounded context = 1 independent team = 1 infrastructure". I think this is easier to figure out what it is givin some example like this.

#### - Modules ❔ are a logic division inside bounded contexts. Usually are kind of namespaces that hosts the parts of the same concept (user,userRepository...)

#### - Ubiquitous language 🗣️ We can think about the language we use within our team that everyone will understand every concept the same way and it is not necessarily like this. I understood like this : User for IT is a role, for accounting department is a bank account and for delivery department is an address. This makes necessary to create a standart and the translation from business to code.

#### - Context Mapping 🗺️ Identify bounded context and, more important, their relationships : 
- Shared kernell are the shared methods, clases, events... which can be used by more than one BC or even more than one module
      
     


### ❗ There are many better posts who give a lot of great info about strategic DDD. There is a recurrent think among DDD experts, and it is about the tremendous mistake avoid strategical DDD is.
---
## some really good sources about strategic DDD
- [tackling-business-complexity-with-strategic-domain-driven-design](https://inside.getyourguide.com/blog/2019/11/18/tackling-business-complexity-with-strategic-domain-driven-design)
- [what are modules in DDD?](https://www.culttt.com/2014/12/10/modules-domain-driven-design)
