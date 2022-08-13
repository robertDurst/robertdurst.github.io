---
layout: post  
title:  "Napkin Math at Work: a Tool for Allocating Engineering Effort"
date:   2022-08-13 00:00:00 +0700   
tech: true
---

I am about four months into my journey at [Spring Health](https://springhealth.com) and it's been a great learning experience thus far! Besides learning the ins-and-outs of Rails, a coworker and I have set up a load testing environment and utilized this to bring forth a few notable perf wins. Those stories are worthy of the official Spring Health Engineering... stay tuned!

However, here's a sneak peak.

## System Awareness

Ice hockey is a rather complex sport. Towards the end of my >15 year "career" playing ice hockey, I became a penalty kill specialist; I specialized in ensuring my team did not give up a goal when playing down a man (or two).

> keep your head on a swivel

![head on a swivel](https://media.giphy.com/media/xT9IgtgWuP0irJKq8E/giphy.gif)

Knowing where everyone was in relation to the player with the puck, or *keeping your head on a swivel*, while deducing the opposing team's system (Brad on defense, the middle of the umbrella powerplay system,  always turns up the shot while Pete, the top goal scorer, keeps trying to sneak back door) required a great sense of situational awareness.

![umbrella powerplay](https://www.crossicehockey.com/wp-content/uploads/2014/04/umbrella-pp.jpg)

At risk of sounding cheesy, I've found killing penalties fairly analogous to developing a performance engineering mindset. Both require proper observability practices (head on a swivel... or [Datadog](https://www.datadoghq.com/)). Both require an understanding of what's typical (Brad never shoots... customers traffic load follows the business day). Both require a mental model of the system (three man high umbrella... frontend react, backend a combination of queues, databases, servers, etc.). And finally, both require some basic stats (on the penalty kill we will always be outnumbered...  5 million requests taking 100ms across 10 containers, etc., etc., etc.).

## Some Basic Stats

![math guy gif](https://media.giphy.com/media/4JVTF9zR9BicshFAb7/giphy.gif)

Yes, basic with a capital B. Basic stats and napkin math go a long way. Unconvinced? Consider the following...

![Bill Nye consider the following](https://media.giphy.com/media/SVL5Dws0bOSgE/giphy.gif)

Let's say a company expects to process 100 million records next week. Since this is a fairly expensive job, they do it in batched tasks using separate resources from their production environment. You observe that, as is, the processing takes so long that it will still be in progress before the next batch of records come the following week.

Let's add some more context.

```
100 million records
1 task/container at a time
100 records/task
4 containers (4 cores each)
350ms per processing per record
500ms per task to save the results
```

Thus, we can quickly guestimate how long processing currently takes.

```
1 million tasks = 100 million / 100 batch size
250,000 concurrently executing tasks = 1 million / 4 containers
4 sec = 350ms * 100 batch size + 500ms to save
1,000,000 sec = 4 sec/task * 250,000 tasks

~11.5 days = 1,000,000 sec / (60 * 60 * 24)
```

Putting on your thinking cap, you dig in to the data your APM presents and discover the following:

* each separate record spends 150ms of processing time encrypting unused data
* during processing, we batch 100 records at a time into a task, where each container can only execute one task at a time and each task is not that computationally expensive

Is it worth the time to implement the possible changes we discovered above? 

Since we don't need to encrypt the useless data, let's cut that (and on a closer look, we actually encrypt two useless fields, so let's cut both of those!!). The time to process a single record is now only 50ms! Furthermore, since each task is independent and computationally cheap, let's reconfigure our cloud cpu resources  to 16 containers, 1 core each. 

With this, let's redo our math.

```
1 million tasks = 100 million / 100 batch size
62,500 concurrently executing tasks = 1 million / 16 containers
0.55 sec = 50ms * 100 batch size + 500ms to save
34,375 sec = 0.55 sec/task * 62,500 tasks
~9.5 hours = 34,375 sec / (60 * 60)
```

Not only do these minimal changes sound trivial to implement, it's clearly worth our time. With just a bit of research and kindergarten level mathematics, we've made a broken process much cheaper, efficient, and bottomline... feasible.

## Conclusion

Napkin math is an art - it requires knowing what's important and what you can gloss over. Used effectively, it's a great tool for determining potential performance wins, ballparking future server traffic load, and quickly checking someone else's numbers. For me, it's be helpful in justifying how I spend my free cycles at Spring. 
