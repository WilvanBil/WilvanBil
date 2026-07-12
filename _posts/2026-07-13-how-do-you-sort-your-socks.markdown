---
layout: post
title: "How Do You Sort Your Socks?"
date: 2026-07-13 00:00:00 +0000
categories: Productivity
description: "A small personal essay about laundry, routine, and the surprising value of changing the order of a simple process."
---

Last night I was doing laundry and got to the socks, which pulled me back to a Discord voice chat conversation I had about SQL optimization. I do not remember the exact situation anymore. What I do remember is that I liked how these conversations kept going after work hours. Maybe that is still work in a way. Or maybe it is just what happens when you spend time with people who make you better at thinking through problems.

I’ve been sorting socks the same way for years: grab one, dig through the pile, find the match, repeat until the basket is empty. It works. It is also a little bit annoying. The kind of annoying you accept because it feels too small to optimize.

## The Sock Problem

Picture a basket full of clean socks. They are all mixed together, and you want pairs before you put them away.

The first version of the process looks like this:

1. Pick up one sock.
2. Search the whole pile for its partner.
3. Repeat.

That sounds reasonable because every sock still gets handled once, right? But it still feels slow. The problem is not just the number of socks. It is the amount of re-scanning, re-checking, and switching my attention back and forth while I still have one sock in my hand. It gets easier once a few pairs are already done, and I have a lot of white socks, so those are easy to spot. The colored ones are the part that slows everything down.

## A Better Way

At some point I changed the process.

Instead of trying to finish each pair as soon as I found one half, I started grouping socks by color first. Then I brought matching socks together. Only after that did I form the final pairs.

That is closer to this:

```sql
SELECT Color, COUNT(*)
FROM SockPile
WHERE Color IN ('Black', 'Blue', 'Brown')
GROUP BY Color;
```

Same basket.
Same socks.
Less chaos.

It was faster, but not because I removed work. It was faster because I changed the shape of the work.

That is usually where the useful optimization lives.

The same idea shows up in other places too. A middleware pipeline is easier to live with when the cheap checks happen first and the expensive work happens later.

## What It Taught Me

I don’t remember the exact query anymore, but I do remember the argument. I remember pushing back with, "It doesn’t matter, you’re still searching the same amount of records." On the surface that sounded logical to me. In hindsight, it was incomplete. The total number of rows may stay the same, but the order of operations changes everything. The same is true with socks: if I force myself to keep re-scanning the pile, the chore feels heavier than it needs to. That is the lesson I keep coming back to. Not every improvement comes from doing less. Sometimes it comes from doing the same thing in a cleaner order.

When something feels slow, I try to ask a different question now:

- Am I doing too much work?
- Or am I doing the work in a messy order?

Sometimes the answer is to remove work entirely. Sometimes the answer is to batch it. Sometimes the answer is simply to stop making the computer, or your brain, do the same search more than once. And that is probably why I like these conversations so much: they start with work, drift into ordinary life, and leave me with a better way to look at both.

## Final Thoughts

So yes, this started with socks.

But the real lesson is broader: optimization is not only about the amount of work. It is also about reducing friction, avoiding repeated effort, and choosing a better sequence.

I still have to do the ironing, though.

Now I’m just wondering if there is a way to batch that too.

If not, I suppose I will have to treat it like a queue and hope the scheduler is kind.
