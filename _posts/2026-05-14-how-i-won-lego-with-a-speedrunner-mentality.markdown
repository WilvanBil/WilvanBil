---
layout: post
title: "How I Won LEGO with a Speedrunner Mentality"
date: 2026-05-14 00:00:00 +0000
categories: [techorama, team4talent, speedrun, conference, dotnet, career, productivity, community]
description: "How a developer conference trivia challenge turned into a speedrunning obsession and a medieval LEGO set."
---

I did not expect a trivia challenge at a developer conference booth to occupy my thoughts between sessions. But somewhere between day one and day two, during breaks and the gaps left by 30-minute sessions, I kept drifting back to a leaderboard, shaving seconds off my time like I was preparing for a world record attempt. I did not have that on my Techorama bingo card.

This is the story of how I won a medieval LEGO set at Techorama. And how getting there reminded me of something I already knew as a developer but apparently needed a game to actually feel: **iteration beats perfection, and a good crew beats going it alone.**

## The Scene

Techorama is a two-day developer conference built around .NET, Azure, and AI. Sessions, talks, and the kind of conversations you only have when you put a few hundred developers in the same building. Between sessions, you visit booths. Companies set up mini challenges with real prizes on the line — a PS5, a Nintendo Switch 2, tech gadgets, and yes, LEGO.

On day one, I walked past the Team4Talent booth and noticed something odd. Three laptops. A small queue. People staring at their screens with the kind of focused intensity usually reserved for production incidents.

I got closer. It was a trivia challenge. Multiple choice questions about .NET, Azure, and a few things specific to Team4Talent. Think: *where in Belgium is Techorama held? What EF Core command applies your migrations to the database? Which Azure resource do you use for Kubernetes?* Nothing impossible if you work with this stack. The twist: you needed to reach **9 points as fast as possible**. Correct answers gave you 1, 2, or 3 points depending on difficulty. You could retry as many times as you wanted. Only your best time counted.

Top 3 on the leaderboard at the end of the conference wins a prize.

I will admit I was nervous going in. That familiar voice in the back of your head asking whether you actually know your own stack well enough. You probably know the feeling. I typed my name and clicked Start.

## First Run

I answered every question correctly, took my time, and finished somewhere around a minute. I submitted my score and checked the leaderboard.

People were finishing in under 30 seconds.

For context: in speedrunning circles, "sub 30" means completing something in under 30 seconds. It is a milestone, a benchmark, a thing to chase. I did not know I was about to spend the rest of the conference chasing it.

---

## Chapter 1 — Know Your Lines

The first thing I noticed was that the question pool was finite. Around 20 to 30 questions that cycled. Which meant I could memorize them.

The answer positions were randomized, so I still had to read each question, but with the answers memorized I could scan for a keyword and click without hesitation. Pattern recognition over careful reading. A few more runs, a mental catalogue of answers, and I was suddenly in sub 20 seconds.

A 40-second improvement. Just from memory.

---

## Chapter 2 — The Sweet Spot

Each question had four answer options. My mouse was starting from wherever it ended up after clicking Start. That meant every run began with a slightly different amount of cursor travel to reach an answer.

The fix was simple: after clicking Start, I moved my mouse to the center of the screen, between options 2 and 3. From that neutral position, any answer was reachable with minimal movement.

It sounds like a small thing. It shaved off another second.

```
┌─────────────────────────┐
│  Option 1               │
├─────────────────────────┤
│  Option 2               │
├··························┤
│         🖱️  ← cursor    │  <- neutral position
├··························┤
│  Option 3               │
├─────────────────────────┤
│  Option 4               │
└─────────────────────────┘
```

I also tried pre-wiggling the mouse while reading a question so I could launch toward an answer faster. That one was a rollback. It cost more time than it saved. Too much noise in the movement, not enough precision. Sometimes you test something, it does not work, and you revert. That is just iteration.

---

## Chapter 3 — Skip the Start Screen

Before every run, you type your name into a text field and click a Start button at the bottom of the screen. The answer options appear centered in the middle.

I had been clicking Start, then dragging my mouse up to the center. A small inefficiency, but it happened every single run.

Sam spotted it: fill in your name, move your mouse to the center between options 2 and 3, and hit **Enter** instead of clicking Start. The button responds to keyboard input. By the time the first question appeared, my cursor was already in position.

That saved another second. We were in sub 16 territory. I say "we" because by this point, a few other people at the conference had noticed what I was doing and started comparing notes.

![Early leaderboard — my best at 15.9 seconds]({{ site.baseurl }}/assets/how-i-won-lego-with-a-speedrunner-mentality/leaderboard-early.png)

*The leaderboard at this point. 15.9 seconds. Top spot, for now. Also note the titles: the game assigned everyone a .NET-flavoured knightly rank at random. Mine happened to be "The Chosen of the CLR". I was not going to argue with that.*

---

## Chapter 4 — The Wrong Answer Meta

This is where it stopped being a trivia challenge and started being something else entirely.

I had been focused on going faster mechanically: less mouse movement, better memory, smarter inputs. But I had not looked at the game itself.

Jurgen, Sam, and I started picking apart the point system. We all knew the goal was 9 points. What we had not stopped to ask was: *how few questions do you actually need to answer to get there?*

The points were structured like this:

- First 3 questions: 1 point each
- Next 3 questions: 2 points each
- Final 3 questions: 3 points each

The order was fixed. You could not skip ahead to the high-value questions.

But you could fail the low-value ones.

Clicking a wrong answer is faster than reading a question and clicking the right one. If the first two questions are worth 1 point and you do not need all of them, blazing past them incorrectly is faster than carefully answering them correctly.

After some testing, we landed on this sequence:

| Question | Action | Running total |
|---|---|---|
| 1-point question | Answer incorrectly | 0 |
| 1-point question | Answer incorrectly | 0 |
| 1-point question | Answer correctly | 1 |
| 2-point question | Answer correctly | 3 |
| 2-point question | Answer correctly | 5 |
| 2-point question | Answer correctly | 7 |
| 3-point question | Answer correctly | 10 ✅ |

Seven questions instead of nine. Two of them answered without thinking. The running total hits 10, one over the required 9, because that is just how the point values land. There is no way to stop mid-question once the 3-pointer is answered.

A "strat," in speedrunning language, is a strategy — a specific sequence of inputs optimized for time. This was our first real strat. It pushed us into sub 15 seconds consistently.

Side note: on a couple of runs I accidentally answered the first two questions correctly because I misclicked. We were truly suffering from success.

#### In hindsight

We were so deep in optimizing the strat we had that we never stopped to question it. When you are in that mode, iteration feels like progress. And it is. But sometimes the biggest gain is not making your current approach faster. It is realizing there was a better approach the whole time.

We kept one 1-point question to cross the threshold before the 2-pointers unlocked. What if we skipped that too? Fail all three 1-pointers, then answer three 2-point questions and one 3-point question.

That is 0 + 0 + 0 + 2 + 2 + 2 + 3 = **9**. Exactly 9. One fewer question. And the 2-pointers are faster to answer correctly than the 3-pointers.

We never tried it. We were too busy getting faster at the wrong sequence.

---

## Chapter 5 — Tab, Space, Go

Jurgen arrived with a keyboard optimization that elevated the whole crew.

The first two questions did not matter. We just needed to get through them as fast as possible. His insight: instead of moving the mouse to answer them, use the keyboard.

The new sequence at the start of every run:

1. Hit **Enter** to start (from the previous optimization)
2. **Tab, Space** — selects the first option on question 1
3. **Tab, Space** — selects the first option on question 2

No reading. No thinking. No mouse movement. Just two quick keyboard inputs and the throwaway questions were gone.

From there, switch back to the mouse for the questions that actually mattered.

Less brain overhead. More reactive. Sub 14 seconds.

---

## Chapter 6 — Three Options Only

The last optimization was mental.

Now that Tab + Space always selected option 1 for the first two questions, I had created a pattern in my head. My brain knew that option 1 was "free". That was the throwaway slot. So from question 3 onward, I mentally removed option 4 from consideration entirely.

If the answer was option 1: Tab + Space.
If the answer was option 2: mouse, slight move up.
If the answer was option 3: mouse, slight move down.

Three movements. Minimal travel. Minimal decision time.

```
┌─────────────────────────┐
│  Option 1  ← Tab+Space  │
├─────────────────────────┤
│  Option 2  ← mouse ↑   │
├··························┤
│         🖱️              │  <- neutral position
├··························┤
│  Option 3  ← mouse ↓   │
├─────────────────────────┤
│  Option 4               │  <- ignored
└─────────────────────────┘
```

Ignoring option 4 meant occasionally missing a question where the correct answer was option 4. Yes, that happened. But the time saved by reducing mental branching on every other question more than compensated for it.

Sub 13 seconds.

---

## The Run

At some point on day two, everything clicked.

I typed my name. Positioned my mouse. Hit Enter. Tab Space. Tab Space. Then I moved through the remaining questions on pure reaction — keywords, recognition, muscle memory. No hesitation.

11.3 seconds. Give or take a fraction. I cannot remember the exact number, but it started with 11.

I looked at the leaderboard. I was at the top.

![The medieval LEGO set I won at Techorama]({{ site.baseurl }}/assets/how-i-won-lego-with-a-speedrunner-mentality/lego-set.jpg)

I walked away with a medieval LEGO set, a group of colleagues I had just spent hours optimizing a trivia game with, and a quiet satisfaction that I had genuinely squeezed every second I could out of something completely ridiculous.

At the closing ceremony on day two, LEGO set in hand, surrounded by friends and (ex-)colleagues, I caught myself thinking:

*What if sub 11 was possible?*

---

## Thanks

Delen Private Bank for sending me to Techorama in the first place. Team4Talent for building something far more addictive than a trivia challenge has any right to be. My speedrunning crew — Anouk, Sam, Egon, and Jurgen — for turning a leaderboard into a collaborative obsession. And everyone who made Techorama 2026 what it was.
