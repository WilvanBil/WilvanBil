---
layout: post
title:  "ChatCompletion 400 OpenAi"
date:   2025-01-23 21:25:42 +0100
categories: Azure OpenAI
---

Like a good boy developer I try to keep up with changes. Even if they're not directly in line with my current project. One of those things is AI. So I headed to my portal.azure.com and ai.azure.com and started deploying models, tweaking with instructions, settings, settings of settings, working with data sources. It's been quite a journey. I developed a simple chatbot (and later integrated it into my Discord bot, maybe worth another blog) but I keep stumbling into the same error.
Status 400. Now we all know what this means, but unfortunately the response did not contain any context or information as to what went wrong. Is it my setting? Is it the rate limit? Have I run out of tokens? Maybe I should change that boolean in that setting or set the float value of this higher. All this tweaking just to get hit by another 400. So I scoured the internet, went through official wiki's, went through readme's, search the repo's for samples or tests that use the same setup as me to eventually lead me to a Github issue with someone that has similar symptoms. In one of those comments user (add user here) provided a little piece of middleware that can provide more contextual information. ( add link to github issue and code that gives more information)
I hope the devs will prepare a new update soon with more information.
https://github.com/Azure/azure-sdk-for-net/issues/47280
https://github.com/Azure/azure-sdk-for-net/issues/47280#issuecomment-2518556663

AzureOpenAIClient
Example code
Add screenshot of initial error
Add screenshot for after implemeting policies.
How can I limit rates? What else can I do to optimize my setup?

Add My code, will remove and refactor the stuff that i dont need in context of the blog

In the beginning of the blog. Add no nonsense solution Followed by the initial blog. So people who just want to go to the solution can go straight into it.
Also add references to the issues and user so people can instantly fix their similar issues.