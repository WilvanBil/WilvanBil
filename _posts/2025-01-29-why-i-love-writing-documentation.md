---
layout: post
title: " Why I Love Writing Documentation"
date: 2025-01-29 12:00:00 +0100
categories: Documentation
---

I love writing documentation. I really do.

As developers, we often focus on the joy of writing code—building new features, optimizing existing ones, and solving challenging problems. But none of that taught me much about **user empathy**. Documentation, however, did.

In my previous blog post [link here], I mentioned **user empathy** briefly. Writing documentation has made me realize just how important it is to step into the shoes of the people who will use, maintain, or extend your code—whether that's your teammates or external developers.

---

## What Documentation Means to Me

To me, documentation is not about copying functional requirements into a wiki or pasting a commit link to explain why something changed. It's also not just a random collection of notes or overly detailed flowcharts (though I do love a good flowchart).

Good documentation is about creating **a clean, structured way to answer the questions people will forget to ask**. It’s a way to offload knowledge and make it accessible—not just for others, but for your future self as well.

---

## Why I Love Documentation

Here’s the truth: I’m terrible at remembering things. My brain has limited capacity, and I prefer to fill it with things that matter to me. About 80% of my mental storage is dedicated to **Tekken frame data**. Another 15% is reserved for my **healing rotation in World of Warcraft**. That leaves maybe 4% for actual code.

This is why I write things down. Whether it’s during a meeting or when solving a tricky bug, I jot down notes to make sure I don’t lose critical information. But note-taking is just the first step. What makes notes useful is **organizing and reflecting on them** so that others (and future you) can benefit.

Documentation serves the same purpose. It's not just for clarity or compliance; it's about building a system of knowledge that reduces cognitive load for everyone. A week after writing a method, I might forget why we added it, but if I’ve linked it to a story or left a clear comment, I can quickly piece things together.

---

## Why Documentation Matters for Developers

Let’s be honest: how many times have you struggled with a poorly documented library? Maybe the README was outdated, the API surface was unexplained, or there were no examples of real-world usage.

Good documentation is invisible—it just works. Bad documentation (or the lack of it) is painfully obvious.

Here’s what developers can do to make their work easier for themselves and others:

1. **Provide a README**:
   - Include installation instructions, a quick-start guide, and examples of common usage.
   - Think of your README as the first impression a developer will have of your project. Make it count.

2. **Write XML Summaries**:
   - For every public class, method, or property, add a summary that explains **why** it exists and **how** to use it—not just **what** it does.
   - For example:

     ```csharp
     // Bad
     /// <summary>
     /// This processes records.
     /// </summary>
     public void ProcessRecords()

     // Better, allthough I don't even like the function name. What does Process even mean?
     /// <summary>
     /// Processes the incoming records by applying necessary business rules and transformations.
     /// </summary>
     /// <param name="records">The collection of records to process.</param>
     /// <returns>A list of transformed records.</returns>
     public IEnumerable<Record> ProcessRecords(IEnumerable<Record> records)
     ```

3. **Think About the Future**:
   - Documentation isn't just for today—it's for the developer who will pick up your code in six months (which might be you). Leave behind breadcrumbs that explain the intent behind your decisions.

---

## My Documentation Tips

- **Double-Check Your README**:
  - Before releasing a library or project, go through the README. Is it clear? Are the examples complete? Does it explain the "why" as much as the "how"?

- **Start Small with XML Summaries**:
  - You don’t need to write a novel, but even a sentence or two can go a long way in helping others (and yourself) understand your code.

- **Apply the Boy Scout Rule**:
  - Leave your code/documentation better than you found it. Even if you’re not the original author, improve it as you go.

- **Tackle Documentation Debt**:
  - We all know about technical debt, but documentation debt is just as real. Treat it as a backlog item and prioritize cleaning it up when necessary.

- **Use Ubiquitous Language**:
  - Avoid inventing terms for concepts that already exist in your domain. If your business calls users "customers," don’t call them "clients" in your code.

---

## Wiki vs. README: When to Use What?

One common question I get is: "Should I put this in a wiki or a README?" Here’s how I approach it:

- **README**:
  - Focused, project-specific documentation.
  - Includes setup, usage, examples, and contributing guidelines.
  - Lives alongside your code and changes with it.

- **Wiki**:
  - Broader, team-wide or domain-level knowledge.
  - Includes architecture diagrams, workflows, and shared processes.
  - Ideal for high-level documentation that applies across multiple projects.

Think of the README as the "quick start" guide and the wiki as the encyclopedia.

---

## Closing Thoughts

Your brain can only hold so much information. Use that space for what matters most to you—whether it’s Tekken frame data, your WoW healing rotation, or something else entirely. For everything else, there’s documentation.

As developers, tech leads, and architects, we owe it to ourselves and our teams to make documentation a habit. Not just for the sake of clarity, but as a way to create lasting impact and align everyone toward a shared understanding.

Start small. Start today. And remember: good documentation is an investment in the future.

---
