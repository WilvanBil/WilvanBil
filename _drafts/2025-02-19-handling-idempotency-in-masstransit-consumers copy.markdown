---
layout: post
title:  "How I (try to) handle idempotency in my MassTransit consumers"
date:   2025-02-19 11:00:00 +0100
categories: MassTransit
description: ""
---

## **From APIs to Messaging – My Journey**

There was a time when **messaging systems** were completely new to me. I started with API-based communication—first **ASMX Web Services**, then **WCF**, and eventually **Web APIs, GraphQL, and gRPC**.

How do these technologies fit together? In my head, they all belong to the same cabinet:  
_“Ways that other applications can communicate with my application.”_  

Then I encountered **message-based frameworks** like **MassTransit** and **RabbitMQ**, and my mental model had to expand.  
Unlike APIs, where **a request always expects a response**, event-driven systems introduce **asynchronous workflows, retries, and, of course, idempotency challenges.**  

This doesn't mean that REST/GraphQL APIs don't need idempotency. (on the contrary), but this is about my experience with idempotency with messaging based systems.

---

## **What Is Idempotency?**

According to [Wikipedia](https://en.wikipedia.org/wiki/Idempotence):

> "Idempotence is the property of certain operations in mathematics and computer science whereby they can be applied multiple times without changing the result beyond the initial application."

💡 **For developers, idempotency means that processing the same message multiple times should have the same effect as processing it once.**  

### **Why Does It Matter?**

Imagine you have a **consumer that processes payments** when an `PremiumApprovedEvent` is received.

```csharp
public class CreatePaymentsConsumer : IConsumer<PremiumApprovedEvent>
{
    public async Task Consume(ConsumeContext<PremiumApprovedEvent> context)
    {
        await ProcessPayment(context.Message);
    }
}
```

Looks simple, right? **But here’s the problem:**  

What if **the same event is processed multiple times**?  

- The message broker **could retry** the message due to network failures.  
- The producer **could have accidentally published it twice.**  
- Your system **could restart mid-processing**, leading to duplicates.  

🔴 **Result:** The customer gets charged **twice** for the same order. The finance team is not happy.  

✅ **Solution:** Idempotency ensures that even if an operation runs multiple times, **the result remains the same**.

---

## **The Problem: Handling Duplicate Events in MassTransit**

I ran into this issue when working on a **MassTransit consumer that processes payments**. The consumer listens for a `PremiumApprovedEvent` to trigger a payment.

```csharp
public record PremiumApprovedEvent(Guid PremiumId, decimal Amount);
```

Every time this event arrives, the system **creates a new payment request**. But soon, we noticed duplicate payments appearing in our database.

🔍 **Possible Causes of Duplicate Events**:

- **Message Retries** – RabbitMQ or MassTransit could retry the message if an acknowledgment isn’t received.  
- **Duplicate Messages from the Producer** – The event publisher might accidentally send the same event twice.  
- **Multiple Consumer Instances** – When scaling consumers horizontally, multiple pods may receive and process the same event.  
- **Application Restarts Mid-Processing** – If the consumer crashes after processing but before confirming success, the message gets reprocessed.
- **Out-of-Order Events** – Events may arrive in the wrong sequence, causing incorrect state updates.
- **Database Transactions Not Committed Atomically** – A crash mid-processing may result in duplicate inserts.
Each of these scenarios **creates the risk of duplicate transactions**. So, how do we handle idempotency in MassTransit?

---

Retry
Redelivery

---

Producer sends same event multiple times, add business checks if it's allowed

---

When working with concurrent consumers. (multiple consumer instances)

```csharp
public class CreatePaymentsConsumerDefinition(IOptions<ConsumerConfig> consumerConfig) : ConsumerDefinition<CreatePaymentsConsumer>
{
    public CreatePaymentsConsumerDefinition()
    
        ConcurrentMessageLimit = 2;
    }
    protected override void ConfigureConsumer(IReceiveEndpointConfigurator endpointConfigurator, IConsumerConfigurator<CreatePaymentsConsumer> consumerConfigurator, IRegistrationContext context)
    {
        // Consumer config
    }
}
```

There's a difference between concurrent consumers vs concurrent pods in AKS. (horizontal scaling)

TODO: Go through each scenario and explain what goes wrong, what we can do to remedy it.
Are there built in measurements that we can take?
Ways to handle idempotency here
---

## **Final Thoughts**

🔹 **Idempotency is crucial** for event-driven systems—always assume messages might be **retried or redelivered**.  
🔹 **Different approaches work in different contexts**—sometimes **database deduplication** is best, other times **transport-level solutions** work better.  
🔹 **MassTransit provides useful tools**, but you still need **a strategy** that fits your business logic.  

**How do you handle idempotency in your event-driven services? Let’s discuss!** 🚀
