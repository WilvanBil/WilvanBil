---
layout: post
title:  "Event Sourcing: Personal Point of View(Projection)"
date:   
categories: Event Sourcing State 
description: ""
---
I only came in contact with event sourcing on a project level on my latest project. Before I was used to simple state based modelling. You had your CRUD actions, a repository that contained your CRUD actions for your specific entity. And you could just execute it. You could easily modify it through code and on the database directly. Sometimes we needed to keep track of history. In a previous project we had an audit table that kept records on what entity was changed, what was the initial value and the new value. But those were kept for auditory (what's the correct word?) reasons.

I did receive a couple of trainings regarding Event Sourcing, coupled together with Domain Driven Design and Event Storming. This was a radically different way for approaching state in models. But those were just what it is... Trainings. Something to keep in the back of your mind where you store a couple of trigger words so you can look it up later again. Yeah, I'm the kind of person that needs to repeatedly do something before I truly get it.

Then on my current project I got my first real taste of event sourcing. We did the whole shebang. Event storming, identifying domain events, consolidating Aggregates, defining entities, bounding(?) bounded contexts, structuring our microservices and creating our read view projections. We chose our frameworks (Marten as ORM/Event Store and MassTransit for Integration Event handling.)

This blog is not about how to set up your first DDD dotnet solution. There are plenty of those online. This is for the people that already started it, trying to get a hang of it, but aren't really "seeing the light". Also it's a bit of a personal story for me on how I see events.

One of the main rules in Event sourcing is that an event is immutable. What does that mean? It means it cannot be changed. Once it has been inserted it can NEVER be changed. Why? Because it's a thing that has happened. It has occurred and it has specific parameters that we knew at that point in time. Now that's not really true, because an event is "only" a record in the database. And with enough privileges you can modify it to change history. But we can't, we shouldn't. It doesn't happen in real life. You can't go back in time and change events eventhough we wish we could. What's done is done. That embarrassing remark you said once in class has happened. That time you tripped and fell in front of your high school crush, your first kiss, The day you bought your own house, when your mother passed away...

These are all events that has happened, all with factual information that has happened with it. All those events shape you. They make who you are right now and how you perceive the world.
Now you can choose to pick select events that has happened in your life and let them shape you and your point of view. Or you can choose to pick all the good events and the bad events that you want to forget and create an entire new perspective on life.

