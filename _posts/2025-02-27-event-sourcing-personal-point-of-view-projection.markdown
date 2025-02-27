---
layout: post
title: "Event Sourcing: Personal Point of View(Projection)"
date: 2025-02-26 10:00:00 +0100
categories: Event Sourcing State Domain Driven Design
description: "Events shape us, just like they shape systems. In event sourcing, events are immutable, but how we project them changes everything. The same is true for life."
---

## If you could rewrite a moment from your past, would you?

There are moments we wish we could rewrite. That awkward comment in class, the PowerPoint presentation we bombed, the goodbye we never got to say. But events, once they happen, are immutable.  
We can replay them in our minds, analyze them from different angles, but we can't change them.

Event sourcing works the same way. In software, just like in life, events are the truth of what happened—unalterable, immutable. But just like in life, the way we interpret those events can change everything.

---

## How I Started with Event Sourcing

My first real experience with event sourcing came on my current project. Before that, I was used to simple state-based modeling. CRUD operations, a repository that handled reads and writes, and if we needed history, we had an audit table—but that wasn't event sourcing.

I had received training on **Event Sourcing, Domain-Driven Design, and Event Storming**, but it never fully clicked. Concepts like **aggregates, event streams, and projections** sounded abstract. It wasn’t until I actually worked on a project level with **Marten** for event storage and **MassTransit** for handling integration events that I began to understand. That’s when I truly saw the **importance of immutability and proper event handling.**

---

## Just Another Event Sourcing Blog?

Earlier this year, I noticed something in the code I was reviewing. **I saw event sourcing misused in code.** A flow replayed events in the wrong order, breaking the logic and obscuring the truth.

It also made me reflect on **real-life events**—how they shape us, how we wish we could change them, and how, in the end, we can’t. But what we *can* change is our **perspective**.
This is not a blog on how to setup Event Sourcing. There are plenty of those out there. This blog is a bit more personal about linking the concepts to real life events.

## Modeling Life as an Event Stream

In event sourcing, entities don’t store just the current state—they store a sequence of events that brought them there.

If our lives were modeled like an event-sourced system, our past would be an event stream:

```csharp
public record EventOccurred(DateTime Timestamp, string Description);
```

Each key moment in life is an event:

```csharp
var lifeEvents = new List<EventOccurred>
{
    new(DateTime.Parse("2002-09-02"), "First day of high school"),
    new(DateTime.Parse("2014-06-30"), "Started my career as a .NET developer"),
    new(DateTime.Parse("2017-07-14"), "Mother passed away"),
    new(DateTime.Parse("2023-10-22"), "Ran my first marathon"),
    new(DateTime.Parse("2025-02-26"), "Published this blog")
};
```

We can't change these events. But we can create different projections based on them.

---

## The Chat Message That Fell Flat: Events Cannot Be Undone

Event sourcing follows an append-only log. You send a message in a chatgroup, thinking it's funny. It isn't. You regret it and in hindsight you wish you could undo it. But in event sourcing, you can’t delete an event—you can only append a new one.

```csharp
public record MessageSent(Guid MessageId, string Content, DateTime Timestamp);
public record MessageDeleted(Guid MessageId, DateTime Timestamp, string Reason);
```

Even if you "delete" a message, someone might have seen it, the original still exists in the event log:

```csharp
var chatEvents = new List<object>
{
    new MessageSent(Guid.NewGuid(), "Haha, this joke will land!", DateTime.UtcNow),
    new MessageDeleted(Guid.NewGuid(), DateTime.UtcNow.AddMinutes(1), "It was insensitive and not funny.")
};
```

The past is immutable, but we can append context to it.

---

## The Breakup: Different View Projections from the Same Events

Read models and projections in event sourcing allow different perspectives on the same event. Two people go through the same breakup, yet their perspectives differ.

```csharp
public record BreakupOccurred(Guid RelationshipId, DateTime Timestamp);
```

But how they view it depends on the projection:

```csharp
public class PositiveProjection
{
    public string Interpretation {get; private set;}

    public void Apply(BreakupOccurred @event)
    {
        Interpretation = "This breakup was a chance to grow and improve.";
    }
}

public class NegativeProjection
{
    public string Interpretation {get; private set;}

    public void Apply(BreakupOccurred @event)
    {
        Interpretation = "This breakup was a painful failure.";
    }
}

var positivePerson = repo.Load<PositiveProjection>(personId);
var negativePerson = repo.Load<NegativeProjection>(otherPersonId);

Console.WriteLine(positivePerson.Interpretation);
//Output: This breakup was a chance to grow and improve.

Console.WriteLine(negativePerson.Interpretation);
//Output: This breakup was a painful failure.

```

One views it as loss, the other as growth.
The event doesn’t change—the way you view it does. It could be the same person, it could be the other person.
In event sourcing, we build different read models from the same set of events, each with its own purpose.

---

## Running a Marathon: Order Matters

Training for a marathon isn’t about a single day—it’s a series of events. Each run builds on the previous one, adjusting pace, endurance, and recovery strategies.

Consider the following events:



---

## Losing a Parent: Events Shape Identity

An entity in event sourcing is the sum of all past events. Some events leave a lasting impact—they don't just modify a single attribute; they **change how future events are processed**.

Let’s model this:

```csharp
public record ParentPassedAway(Guid ParentId, DateTime Timestamp);
```

A person, like an entity, is shaped by their life events:

```csharp
public class Person
{
    public List<object> EventStream { get; } = new();
    public bool HasLostParent { get; private set; }

    public void Apply(object @event)
    {
        EventStream.Add(@event);

        if (@event is ParentPassedAway)
        {
            HasLostParent = true;
        }
    }
}
```

If this event exists, the person is permanently altered. Future experiences—**weddings, career milestones, hardships—are all viewed differently**.

### **Why This Matters in Event Sourcing**

Some events are just state changes (e.g., a name change, an address update). They modify attributes but don’t fundamentally change how an entity functions.  
Other events are foundational—they don’t just add data; they redefine the context for every future event.

In event sourcing, this means:

- If a `ParentPassedAway` event is in the event stream, it cannot be undone.
- Future events are processed differently because the entity (the person) is now in a new reality.
- It doesn’t just affect the past state—it impacts **how all future events should be handled**.

### **Example: How Future Events Change Based on This Event**  

Let's say the entity has an event for `GraduationCeremonyAttended`.

#### **Before This Event:**

```csharp
var person = new Person();
person.Apply(new GraduationCeremonyAttended(DateTime.Parse("2023-06-10")));
Console.WriteLine("Parent attended the ceremony.");
```

**Outcome:** The event is logged, the parent attends.

#### **After This Event:**

```csharp
person.Apply(new ParentPassedAway(Guid.NewGuid(), DateTime.Parse("2023-04-15")));
person.Apply(new GraduationCeremonyAttended(DateTime.Parse("2023-06-10")));

if (person.HasLostParent)
    Console.WriteLine("Graduation felt different without them.");
```

**Outcome:** The same graduation event happens, but it’s now processed with a different context.

---

## Final Thoughts: How We Choose to See Events

Everything that has happened to you—good or bad—has shaped you. But what defines you isn’t just the events themselves. It’s how you interpret them.

In event sourcing:

- Events are immutable. We cannot change the past.
- Events must be appended in the correct sequence. You can't run if you don't know how to walk.
- Projections shape interpretation. We choose how we view history.
- Replaying events lets us learn. The past teaches us the future.

So, what’s your projection? Are you stuck on a single event that defines your entire outlook? Or are you going to take the same data and create a new, empowering view?

Because, just like in software, the events of our lives are immutable—but our perspective is ours to shape.
