layout: page
title: "event-storming"
permalink: /event-storm

# Event Storming 🕴️ 🧑‍💼 👨‍💻
--- 
Event storming is a meeting in which business experts, IT department and developers try to discover the app main functionalities.

###### ingredients:
- Tons of different colors sticky notes. Each color matches any part of our app following a standart.  🟧 🟦 🟪
- A large wall or adhesive blackboard, in order to provide a surface to draw and place sticky notes 
- A pomodoro timer 🍅
- In order to create a confortable and chill environment, usually is good to bring caffe or some food.(Don't be tight-fisted) ✊ ❎

_______
1. The first step is, in small mixed groups, ask them to think about some action they do during their day-by-day working, write it in the past tense on an orange sticky note and post it on the wall. E.g :"we need to create a lot of invoices" so they have to write something like "an invoice was created".
What we are really doing here is creating our domain events. 

2. Once we have the domain event, the next step is post the _command_ which release the event itself. On a blue sticky note, must be written kind of order like:
"Create an invoice". It can be performed by any kind of actor, human or not. 👽

3. Now we have to identify who is creating our events. Using a small yellow sticky note, it only must be written the actor, for instance user, who releases the action. It's affordable (and lovely) to draw a sticky man on the post-it

4. Once we have these elements put together, we have our aggregates, which must be identified with large yellow sticky notes.

5. Some other post-it colors code is :
    - purple -> business rules
    - pink -> some third party software e.g.: any payment platform
    - green -> User's view 

This must be done within a pomodoro style, in 25 minutes sprints per group, in order to keep it dynamic

![example of an event storm wallboard](https://github.com/jmiquis/TFG-Theoretical/blob/master/docs/images/Event_Storming_example_process.jpg)

example of an event storm wallboard https://en.wikipedia.org/wiki/Event_storming

### ❗This is a ridiculously light glimpse about event storming . If you want to get deeper on it, there are loans of info about it. Just google it.

---

## Some interesting content about event storming


#### Alberto Brandollini's talk(Event storm creator) 
[![alberto brandollini talk](https://img.youtube.com/vi/mLXQIYEwK24/0.jpg)]

- https://medium.com/@springdo/a-facilitators-recipe-for-event-storming-941dcb38db0d
- https://www.eventstorming.com/
- https://es.slideshare.net/ziobrando
