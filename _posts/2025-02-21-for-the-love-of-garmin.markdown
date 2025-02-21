---
layout: post
title: "For the Love of Garmin, Please"
date: 2025-02-21 20:00:00 +0100
categories: Garmin Second Brain Bot Api Strava
description: "Garmin makes great fitness trackers, but getting my own data shouldn’t be this hard. Unlike Strava’s open API, Garmin locks data behind a business application. I break down my struggle to integrate it into my bot, failed workarounds, and why I’m still stuck downloading .csv files."
---

I built a Discord bot to act as my [Second Brain](https://www.buildingasecondbrain.com/), a place to store notes, ideas, and random thoughts. But why stop at text?
What if it could track my daily habits as well? Steps, sleep, workouts, everything that makes up my routine.

---

## **Strava? No Problem. Garmin? A Problem.**  

I use **[Strava](https://www.strava.com/)** to track my runs. Their **[API](https://developers.strava.com/docs/reference/)** makes it simple to retrieve data.

### **Getting Strava Data is Simple**

Strava makes things easy. You sign up, create an app, and get an API key.
With that, you can start pulling data immediately—no lengthy approvals, no waiting.
The entire process takes minutes, and their [docs](https://developers.strava.com/docs/getting-started/) explain it well.
Compare that to Garmin, which feels like you need a business proposal just to access your own steps.

---

## **Garmin and Their Locked API**  

I use a **Garmin Forerunner 255** as my daily smartwatch. It tracks my runs, syncs with Strava, and, most importantly, **monitors my sleep**.
I used to think sleep tracking was just a gimmick. Then I hit my 30s, and suddenly, five hours of sleep meant my entire next day was ruined. Now, I obsess over my sleep quality. Am I getting enough deep sleep? Does a late-night coding session destroy my recovery? These are the kinds of insights I want from my bot.
And that’s where the problem starts. Garmin does not provide an easy way to access this data for a developer.

Garmin does have an **[Activity API](https://developer.garmin.com/gc-developer-program/activity-api/)**.
But before you get too excited—it's not as simple as Strava.
You can't just create an app and start pulling data.
Instead, Garmin makes you apply for access like you're a corporate partner. That means filling out an **[Access Request Form](https://www.garmin.com/en-US/forms/GarminConnectDeveloperAccess/)**.

---

### **Why Do I Need a Sales Region to Access My Own Data?**  

The form asks for:  

- My **company name** (I’m just a guy with a bot).  
- My **Primary Sales Region** (I don’t have a sales region).  
- Whether I’m making a **commercial product** (No, I just want my sleep data).  

This is not how an API for personal fitness data should work. Why is my own data locked behind a form that feels like an enterprise contract?
I really hope Garmin releases a **consumer-friendly API** someday, but in the meantime…

---

## **The Workarounds (Or, How I Tried to Hack My Own Data)**  

### **Checking DevTools for Hidden API Calls**  

I opened a browser, logged into **Garmin Connect**, opened **DevTools**, and watched the network requests.  

And there it was:
`https://connect.garmin.com/sleep-service/stats/sleep/daily/2025-02-15/2025-02-21`

A **GET request returning JSON**? My heart skipped a beat.  
Could it really be this easy?

If I could just pass an `Authorization` header, I should be able to retrieve my sleep data, right?

---

### **Testing in Postman**  

I grabbed my Bearer Token, copied the request into Postman, and hit Send.

404 Not Found.

Maybe I missed something. I tried copying every header Garmin Connect used.
Still nothing. That’s when I realized—this wasn’t just a missing header issue.
Garmin was actively blocking API calls outside of their ecosystem.

At this point, this isn’t fun anymore. This is not how I want to retrieve a simple `JSON` telling me I slept terribly.

---

### **Browser Automation**  

Some developers scrape sites using **Puppeteer or Selenium**—automating a browser session to grab data.  
I haven't delved into headless browsers yet, but if I had I could:

1. Log in to **Garmin Connect**  
2. Navigate to my **sleep stats**  
3. Extract the `JSON` response  

---

## **For Now, Back to .CSV Files**  

So here I am, manually downloading `.csv` files on Garmin Connect like a chump (I'm sorry, but I really love this expression).

Until Garmin **releases a real consumer-grade API**, I’ll keep:  

- Clicking through **Garmin Connect** website
- Downloading my `.csv`  
- Wishing for a better future  

If anyone has **figured out a better way**, please let me know.

---

## **Final Thoughts**  

If I’m generating the data, I should be able to access it. Strava gets this. Garmin? Not so much.
Until something changes, I’ll keep downloading .csv files manually and hoping for a better future.
Have you figured out a better way? Let me know.
And if someone from Garmin is reading this—please, pass it along.

I still love my **Garmin Forerunner**, but seriously—just let me access my own data.  

---

## **Next Steps**  

1. Explore **alternative Garmin integrations** (maybe third-party services?).  
2. Test **Puppeteer or another automation tool**.  
3. Investigate if I missed something in Garmin’s API docs.  

Until I find a better way, I’ll sleep on it (and track it as well).
