---
layout: post
title:  "How I (try to) handle idempotency in my MassTransit consumers"
date:   2025-02-12 11:00:00 +0100
categories: MassTransit
description: ""
---

There was a time that messaging was new to me. I started with API technologies in asmx web services, WCF and eventually moved on to Web Api, GraphQL, gRPC.
What's this group called? Api's? Service-Oriented Architecture technologies? Communication Frameworks? In my head there's a cabinet that groups these under "Ways that other applications can communicate with my application".
I had no experience with messagebrokers, message based frameworks until a recent project. We implemented MassTransit together with RabbitMQ and used the message based system to supplement our event driven micro services. But this blog is not about implementing MassTransit with RabbitMQ. MassTransit has an excellent quick start guide and documentation https://masstransit.io/ and a superhelpful Discord https://discord.com/invite/YEUrGcDP?utm_source=Discord%20Widget&utm_medium=Connect for more inquiries. You rock PhatBoyG. btw this is the best username I have ever read.

This entry is about handling idempotency.

TODO: Add example where I encountered idempotency


What is idempotency?
TODO: Add definition of idempotency here

give scenario:
Imagine you have a consumer that creates payments towards a store.
Something like PaymentsConsumer: IConsumer<OrderCreated> 

It receives an OrderCreated event and will then create a payment for that store.

All is good and well, however, what happens if another OrderCreated event comes in with the exact same parameters? Maybe it's a new order, maybe it's the same order? What do we want to happen?

What if the same event is processed multiple times?

The message broker could retry the message due to network failures.
The producer could have accidentally published it twice.
Your system could restart mid-processing, leading to duplicates.

🔴 Result: The customer gets charged twice for the same order. The finance team is not happy.

✅ Solution: Idempotency ensures that even if an operation runs multiple times, the result remains the same.
Stuff with concurrency, multiple consumers that can receive the same message( message content)
stuff with retries, failures in the code or outages can make sure that a message can't be fully processed

Ways to handle idempotency here.

The issues that I encountered
solutions
Tips