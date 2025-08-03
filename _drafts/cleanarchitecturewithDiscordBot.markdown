---
layout: post
title: "I made a Discord bot, Clean Architecture style"
date: TODO
categories:
description: ""
---

So I've made a Discord Bot. It was a simple bot, it had a main project and some extra projects each for a different functionality.
Then I thought to myself, how can I make this more difficult?
I know, let me try to refactor it into a clean architecture style solution.
Complete with tests, good seperation of concerns, and just clean clean cleanliness in all the right ways.
Was it worth it?
I don't know.
Maybe I thought it would help me with my corporate coding to see the advantages.
But for now I've been struggling to keep it up.
Dependencies and splitting the existing projects into the correct Domain, Application, Infrastructure code was not easy. Especially when you want to use CQRS.
So I took it one project at a time. One thing that was also really important is the Discord interface, if you compare it to a GraphQL endpoint with Hotchocolate or a basic rest controller, you're sure what the entry points are and how you should handle them.

With Discord Client (Discord.NET) it's a bit trickier. Wel not really, but yes it is.
you need to instantize a client, add listeners to the correct events to make sure that your application logic is hitting correctly. Seperation between InteractionService and SlashCommands as well, you could picture this as your controller endpoints. It's been challenging but it has been quite fun.
I'm loving the fact that you can take any project and try to morph it into an architecture that you've learned at work or at a conference. Another thing that keeps me worried is feature flags. Now I just comment out the servicecollectionextensionmethod in the main program if I want to disable a functionality. I have to think a bit harder on how to do that when I refactor everything to a Clean Architecture state.
