---
layout: post
title:  "SRECon Emea 2025 Submission"
date: 2025-05-21
tech: true
---

## I am not an SRE

After starting the team and helping lead the development of Spring Health’s in-house provider availability system (a story that deserves its own post), I officially transitioned into an SRE role in mid-2024. My mission: to make Observability Driven Development the default. It’s an ambitious culture shift and one that leans more on the socio than the technical. That makes it a challenge, but also a rich learning opportunity (all problems are people problems at the end of the day, right???).

As part of this move, I’ve been leading our internal effort to adopt SLOs. This is actually our fourth attempt. Since I already used my learning budget on [Local First Conf](https://www.localfirstconf.com/), I needed a different path to get to [SRECon Emea 2025](https://www.usenix.org/conference/srecon25emea). So I’m going the speaker route and leveraging this story of _our journey to SLO Readiness_ as my way to get there.

Today I officially submitted my talk proposal! While I very well might not be accepted, it's the first step in the journey towards becoming a speaker and really a participant in not just SRE at Spring, but the entire SRE community. Excited!

## Submitted Abstract for 

Below is my abstract. If I am accepted, will upload my full talk here!

***

**Title**: Run, Walk, Crawl, or How We Failed Our Way to SLO Readiness

Scaling an SRE team in a hypergrowth, resource-constrained, product focused startup is a uniquely challenging endeavor. When velocity is paramount and observability is minimal, it's easy to fall into a “no bug tickets, no complaints, no reliability issues” mindset. At our company, success almost became our demise. Each January, contract go-lives and benefit turnover drove massive, anomalous traffic spikes, setting new baselines for the year. January 2022 was the tipping point. That year, traffic surged well beyond system limits. Only through an all-hands-on-deck effort, led by the newly formed platform team, did we narrowly avoid disaster.

Since then, we’ve experienced multiple similar near-misses. Each time, we came to the same realization: proactive reliability work, while conceptually valuable, doesn’t succeed without foundational maturity. For example, most of us would agree that paging on alerts is a powerful way to run production systems responsibly. But we can’t just declare perfect, no-false-positive alerting into existence. Without prerequisites like baseline observability, clear ownership, and dedicated attention, a paging culture becomes just another well-intentioned idea that quickly loses momentum.

In this talk, we’ll walk through a specific case study of this phenomena at our company: SLO unreadiness, or, how we failed three separate times to get SLOs off the ground, despite increased maturity and motivation at each step. Each failed attempt surfaced another missing foundation, from lack of insight into nominal system behavior to unclear ownership and lack of time for follow-through. On our fourth attempt, with key maturity markers in place, we finally gained traction.

We’ll use this case study to propose a simple framework: four checkpoints we now see as essential signals of readiness for any serious reliability initiative:

1. *basic observability*: the ability to see and measure what matters
2. *an understanding of nominal*: knowing what “normal” looks like in your systems.
3. *a concept of ownership*: accountability via defined service and team boundaries
4. *protected time*: a dedicated resource who can focus long enough to make it stick

This framework, while developed in the context of SLOs, has echoed across other reliability efforts, from development of a homegrown autoscaler, to a standardized incident response process. We believe it generalizes. Our goal is to help others avoid the churn and missed opportunities we faced by recognizing these hidden prerequisites early, especially in fast-moving, under-resourced environments.

***

While `Hope is Not a Strategy` may be _the_ SLO saying, hopefully I follow up soon with good news... wish me luck!
