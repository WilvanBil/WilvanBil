---
layout: post
title: "FirstOrDefault vs SingleOrDefault: Performance vs Semantics"
date: 2025-04-09
categories: LINQ Performance Semantics CodeReview
description: "Do you care more about getting results fast, or getting them right? Let's talk about LINQ, readability, and why the difference between FirstOrDefault and SingleOrDefault matters more than you think."
---
Do you care more about getting results fast, or getting them right? Let's talk about LINQ, readability, and why the difference between FirstOrDefault and SingleOrDefault matters more than you think.

## Would You Check the Whole Box?

If someone asked you to pick the item with a specific characteristic from a box, would you:

- Grab the first item that matches the description?
- Or inspect every item in the box and ensure that you've found the correct match?

What you choose says something about your priorities: **speed** or **certainty**.

This is the exact choice we face in LINQ: `FirstOrDefault()` vs `SingleOrDefault()`.

---

## My Perspective

I review a lot of code. I read more than I write—and when I read, I value **clarity**.

Code is language. And with language, **semantics** matter.

> _Semantics_ (n.): the study of meaning in language, logic, and programming.

In code, semantics = **intention**. It's the difference between _"find the first that fits"_ and _"ensure there’s exactly one match."_ It makes code easier to read, understand, and trust.

---

## When FirstOrDefault Is a Code Smell

One of the most common pieces of feedback I leave during pull requests is this:

> Why are you using `FirstOrDefault()` here? Shouldn’t this be `SingleOrDefault()`?

If you're querying for something that **should only exist once**, then `SingleOrDefault()` communicates your intent better.

```csharp
// This says: "Give me the first match, even if others exist"
var result = items.FirstOrDefault(x => x.Type == "Active");

// This says: "There should only be one. If more, something's wrong"
var result = items.SingleOrDefault(x => x.Type == "Active");
```

---

## The Counterargument: Performance

A teammate once pushed back:  
> "But `FirstOrDefault` is faster!"

Fair. So let's measure.

### Benchmark

I ran a benchmark with 100,000 items in memory.

| Method          | Mean     | Error    | StdDev   |
|---------------- |---------:|---------:|---------:|
| FirstOrDefault  | 22.86 µs | 0.272 µs | 0.227 µs |
| SingleOrDefault | 41.97 µs | 0.633 µs | 0.561 µs |

`SingleOrDefault` is indeed **almost twice as slow**. But let’s add some context.

---

## Semantics vs Optimization

Let’s say you're querying a primary key (`Id`). The collection is guaranteed to contain a unique value.

```csharp
// Performance-driven
var entity = items.FirstOrDefault(x => x.Id == request.Id);

// Semantics-driven
var entity = items.SingleOrDefault(x => x.Id == request.Id);
```

Yes, FirstOrDefault is faster.  
But the question is: **what does your code say to the reader?**

With `SingleOrDefault`, you make a promise:

- There should only ever be one.
- If more exist, something is wrong.

That intent is invisible with `FirstOrDefault()`.

---

## My Stance

Performance is great.  
But in my opinion never at the cost of intent.

If you're dealing with large datasets or working in performance-critical sections, profile and optimize **when needed**.

But in most business applications, **clear semantics** pay off more in the long run. It will lead to better code reviews, fewer bugs, and easier maintenance.

---

## TL;DR: Choose Clarity First

- Use `FirstOrDefault` when you're okay with multiple matches but only care about the first.
- Use `SingleOrDefault` when you expect only one match—and want the code to reflect that.

The LINQ method you choose isn't just about how performant the code runs.  
It's about what the code says.

So… what are you really trying to say?
