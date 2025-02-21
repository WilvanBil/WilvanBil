---
layout: post
title:  "How I (Try to) Handle Idempotency in My MassTransit Consumers"
date:   2025-02-19 11:00:00 +0100
categories: MassTransit
description: "A deep dive into handling idempotency in MassTransit consumers—why it matters, where things go wrong, and how to fix it."
---

## **From APIs to Messaging – My Journey**

There was a time when **messaging systems** were completely foreign to me. I started in the world of API-based communication—first **ASMX Web Services**, then **WCF**, and eventually **Web APIs, GraphQL, and gRPC**.

How do these fit together? In my head, they all belong to the same category:  
_“Ways that other applications can communicate with my application.”_  

Then I encountered **MassTransit** and **RabbitMQ**, and suddenly, things weren’t so simple anymore. Unlike APIs, where **a request expects a response**, event-driven systems introduce **asynchronous workflows, retries, and idempotency challenges.**  

This blog is about **idempotency in messaging**—why it matters, what can go wrong, and how I (try to) solve it.

---

## **What Is Idempotency?**

According to [Wikipedia](https://en.wikipedia.org/wiki/Idempotence):

> "Idempotence is the property of certain operations in mathematics and computer science whereby they can be applied multiple times without changing the result beyond the initial application."

For developers, idempotency means that **processing the same message multiple times should have the same effect as processing it once**.

---

## **The Problem: Handling Duplicate Events in MassTransit**

Imagine you have a **consumer that processes payments** when a `PremiumApprovedEvent` is received.

```csharp
public class CreatePaymentsConsumer : IConsumer<PremiumApprovedEvent>
{
    public async Task Consume(ConsumeContext<PremiumApprovedEvent> context)
    {
        await ProcessPaymentAsync(context.Message, context.CancellationToken);
    }
}
```

Looks simple, right? But here’s the problem:  

### **What if the same event is processed multiple times?**  

- The **message broker retries** the message due to a network failure.  
- The **producer accidentally publishes the event twice**.  
- Your system **restarts mid-processing**, leading to duplicate processing.  

🔴 **Result:** The customer gets charged **twice**. Well maybe not twice but definitely not only once. As a result the finance team is not happy.  

✅ **Solution:** Idempotency ensures that even if an operation runs multiple times, **the result remains the same**.

---

## **Why Duplicate Events Happen in Messaging Systems**

At first, I assumed **duplicates were a rare edge case**.  
Then reality hit me. **Duplicate messages happen all the time**. Here’s why:

- **Message Retries** – MassTransit retries failed messages automatically.  
- **Duplicate Messages from the Producer** – The same event may be published twice due to lack of validation on producer side.  
- **Application Restarts Mid-Processing** – If the consumer crashes **after** processing but **before** acknowledging success, the message gets retried.  
- **Out-of-Order Events** – Events might arrive in an unexpected sequence, leading to incorrect state updates.  
- **Database Transactions Not Committed Atomically** – A failure before committing can lead to duplicate inserts.

Every one of these scenarios **introduces the risk of duplicate transactions**. So, how do we prevent them?

---

## **How I Handle Idempotency in MassTransit Consumers**  

There’s no **one-size-fits-all** solution, but here’s how I (try to) handle idempotency.  

### **1️⃣ Deduplication Using a Database Check**

One of the easiest ways to ensure idempotency is **storing processed message IDs** in the database.

```csharp
public async Task Consume(ConsumeContext<PremiumApprovedEvent> context)
{
    if (await _dbContext.ProcessedEvents.AnyAsync(e => e.MessageId == context.MessageId))
    {
        _logger.LogInformation("Duplicate event detected, skipping...");
        return;
    }

    var payment = new Payment(context.Message.PremiumId, context.Message.Amount);
    _dbContext.Payments.Add(payment);

    _dbContext.ProcessedEvents.Add(new ProcessedEvent { MessageId = context.MessageId });
    await _dbContext.SaveChangesAsync();
}
```

🔹 **Pros**: Simple, reliable, doesn’t require infrastructure changes.  
🔹 **Cons**: Requires a database write for every processed event.  

---

### **Business Logic-Based Deduplication**

Sometimes, **idempotency should happen at the business level**.

For example, when creating payments, we can check if a payment already exists **before inserting a new one**.

```csharp
if (await _dbContext.Payments.AnyAsync(p => p.PremiumId == context.Message.PremiumId))
{
    _logger.LogInformation("Payment already exists, skipping...");
    return;
}
```

🔹 **Pros**: Business-driven, avoids unnecessary operations.  
🔹 **Cons**: Needs careful handling of **partial processing failures**.

---

## **Final Thoughts**  

Idempotency is one of those things that seems **obvious in theory** but **frustrating in practice**.  
It’s easy to say, “Just make it idempotent,” but reality is messy. Messages get retried, failures happen, and distributed systems are unpredictable.

Here’s what I’ve learned:  

- **Expect duplicates.** Don’t assume a message is only processed once.  
- **Use database deduplication where it makes sense.** But be mindful of performance.  
- **Leverage MassTransit’s built-in tools** like `InMemoryOutbox` or message tracking.  
- **Consider the business rules.** Sometimes, idempotency logic should live in your domain.  
- **Tuning concurrency matters.** If duplicate processing is causing issues, limit concurrent consumers.  

What are your best practices for handling idempotency in event-driven systems?  
Let’s discuss—I’d love to hear how others tackle this challenge.  

Now, if only I could make my brain idempotent so I stop forgetting why I wrote this blog in the first place.  
Oh well, at least next time I can just `Ctrl+K` search for it. 😉  
