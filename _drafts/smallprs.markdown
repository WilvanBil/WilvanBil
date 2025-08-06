---
layout: post
title: "How to keep your PR small"
date: TODO
categories:
description: ""
---

How small should a PR be?
How big should a PR be?
Like I mentioned many times before. I review a lot of code.
So it's always a magical journey when I click that PR link and see how many changed files there are.
However I've noticed that sometimes I don't mind 20 files, and sometimes I look in terror at one file.
So what's the deal?
How come one file can be mentally more demanding vs 20 files. It's the content.

Let's say you needed to add a property and handle some business logic.
Let us count.

1. Update Domain model
2. Add/Update DomainEvent to handle changes
3. Add/Update IntegrationEvent because microservices and EDA
4. Update Command
5. Update Query
6. Update CommandHandler
7. Update QueryHandler
8. Update GraphQL type, because we want to explicitly expose what type
9. Domain Test
10. Unit Test
11. Integration Test
12. Expanded docs
13. Graphql schema update in angular
14. angular service
15. angular component ts
16. angular component html
17. angular test (ok I admit I don't do this, but I'll be better)

The reason why it was easier to read to me is because it was easy for me to follow and understand the changes.

Compare this with one file that has 900+ lines with 11 services injected.
Even tho it's one file, you see red and green all over the file. You can't follow the changes and the reasoning behind it.
Now this has a lot of issues. And the most simple one is...
split it up. SOLID is still a thing that works.
I can't keep up with all these changes of my team. I'm only one man. I can't oversee all changes. It frustrates me. Maybe this should be a seperate blog.

What's the sweet spot? 1 file? 5 files? 10 files?
What are some things that could help keep a PR small, regardless of the story.
