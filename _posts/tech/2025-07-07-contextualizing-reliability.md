---
layout: post
title:  "Contextualizing Reliability"
date: 2025-07-07
tech: true
---

Spend a few minutes in the SRE community and you will surely hear: _"reliability is the most feature."_ From the [Google SRE Workbook](https://sre.google/workbook/reaching-beyond/):

![SRE Book on Reliability](assets/images/reliability_is_the_most_important_feature.png)

The argument is sound and, as stated, _"people don't normally disagree."_ However, what does this look like in practice?

![doing math](https://media1.giphy.com/media/v1.Y2lkPTc5MGI3NjExeXo5bmU1OWg3azJvcWk0dGdya2pjaXM5OHF4aGpocHJjeGdsODdrbyZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/ne3xrYlWtQFtC/giphy.gif)

This morning I spent some time contextualizing this argument with numbers.

***

### Setting the Scene

Consider a typical web service. This service has users and we'll make the following assumptions:

1. we aim to serve 100% of users
2. users are `aware of` and `have access to` the site
3. while users have their own reliability issues (i.e. network outages, slow connections, etc.), we'll ignore those for now

### Basic Definitions

**Intent**: the user has the intention, or desire, to use the site.

**Able**: the user, when navigating to the site, has the ability to accomplish their goals and extract value from it. Here `able` will function as a synonym for `reliable`.

### The Interplay of Intent and Ability

As it stands, we have the ideal, 100%, and then reality, the combination of those with intent and the ability to use the site: `100%` of those aware and with access
can use the site, `X%` of those have the intent to use the site, and `Y%` of those are able to use the site.

Mathematically this looks like this:

```
ACCESS: 100%
INTENT:   X% = ACCESS - THOSE_WITHOUT_INTENT
ABLE:     Y% = X% - THOSE_WITHOUT_ACCESS
```

Visually:

![total, intent, able chart](assets/images/total_intent_able_chart.png)

Which is based on the following truth table:

![intent, able truth table](assets/images/intent_access_truth_table.png)

Considering the extreme cases, lack of intent or lack of ability, we get:

**No ability:** if 0% of those with intent have the ability, 0% will access. Thus, even if 100% of users have the intent (i.e. design, marketing, engineering, etc. have done their job exceptionally well), no one can access the site.

**No intent:** if 0% of users have the intent of using the site, 0% will access. Thus, even if the site is completely reliable, if no one wants to access it, no one will.

In reality we have some combination of the two.

### The Don't Leave Any Money on the Table Principle

At the end of the day, we live in a capitalist society. _Most_ web services provide some value to the user in exchange for time and/or money (which then, in turn, pays your salary). So, business leaders desire to convert as much of their addressable market to customers. In business lingo, it's something like this:

![tam, sam, som visual](assets/images/tam_sam_business.png)

[Image source.](https://blog.hubspot.com/marketing/tam-sam-som)

However, converting 100% of users with access to 100% with access and intent is a challenging (and maybe impossible?) endeavor. It would involve turning the following triangle into a square? 🤔

![user funnel](assets/images/user_funnel.png)

[Image source.](https://www.resultics.com/optimizing-performance-digital-funnel-google-analytics/)

As a site reliability engineer, designing an effective user funnel is outside my wheelhouse. 

However, I do believe the following.

> If we've invested the time and money to achieve a decent conversion rate, we are, up to a certain point, wasting resources if we leave any users with intent unable to access our site.

No site is perfectly reliable. In fact you don't want it to be ([each extra nine of reliability is exponentially expensive](https://thenewstack.io/the-hidden-costs-of-chasing-five-nines-in-availability/)). However, there is a threshold of level of service that your users expect, which, if not met, results in missed business. Defining that threshold ([service level objective](https://en.wikipedia.org/wiki/Service-level_objective)) based on a decent proxy of the user experience ([service level indicator](https://en.wikipedia.org/wiki/Service_level_indicator)) is not easy ([heck... there is a whole book about this stuff](https://www.oreilly.com/library/view/implementing-service-level/9781492076803/)). However, as hard as it is, [service reliability engineering](https://sre.google/sre-book/table-of-contents/) is a _science_. There are principles, best practices, patterns, and existing precedents (across industry domains, [medicine](https://relyence.com/2024/08/19/what-is-reliability-engineering/), [bridges](https://www.fhwa.dot.gov/bridge/pubs/hif19093.pdf), [airplanes](https://sassofia.com/blog/the-role-of-reliability-engineering-in-aviation-maintenance-systems/)) we can follow.

### Conclusion

Based on the above, any discrepancy between the level of user intent and the level of user ability is, in my opinion, **money on the table**. So, circling back to our original point of investigation, reliability is _the most important feature_: without it, the rest of your organization’s work loses its effectiveness and momentum and in the worst case, is _completely wasted_.
