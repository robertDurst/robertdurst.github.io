---
layout: post
title:  "OpenClaw - Hello Bob! - A First Take."
date: 2026-02-16
tech: true
---

*"This is a piece of garbage... are you sure you want it"* - IT at a startup firesale as I try to pay $50 for one of the weathered Mac Minis.

Well, more than 8 years later, it turns out not only does `ubuntu` run well on it, it's actually got sufficient specs to run an [OpenClaw](https://openclaw.ai/) assistant. 

**Hardware Specifics:** Mac Mini 2012 (A1347) - Intel Core i5-3210M, 4GB RAM, 98GB disk

**Operating System:** Ubuntu 22.04 LTS

So the goal here was to:
1. see what all the hype is about
2. see if maybe this is the one thing I've always needed to actually adopt Trello...

My setup.
- **Primary model:** `anthropic/claude-opus-4-6` (_edit recently downgraded_)
- **Fallback model:** `openai/gpt-5.2` (_edit recently downgraded_)
- **Web search:** Brave Search API
- **Task management:** Trello via a custom OpenClaw skill
- **Messaging:** Telegram bot (locked to a single allowed user ID)
- **Orchestration:** Docker Compose, running as a single gateway container

***

## Some Lessons

Probably 1k+ blog posts about "how OpenClaw is a game changer", "OpenClaw just replaced ____", etc. This is not a hype post, just a random list of things I learned setting this up this weekend.

### Lesson #1: You Need Cost Controls on Day One

Within the first 24 hours I burned roughly $20 in Anthropic API credits - I told Opus 4.6 to "do research" on some topics. Opus is powerful but expensive and when you give it open-ended research tasks, it happily spawns tool calls, reads files, searches the web, and generates long responses. **I keep heavy tasks to other interfaces: ChatGPT, Claude UI, or Claude Code (I have the max subscription).** Also attempted to codify this within the `TOOLS.md` file: _"Be lean with Anthropic API. Minimize tool calls, batch operations, avoid reading huge files unnecessarily. Spawn sub-agents for heavy research instead of doing it inline."_

Basically:
> API Keys + Tokens != Claude Code Max

IDK why, but this initially caught me off guard.

### Lesson #2: Rate Limits and Fallback are Not Plug-and-Play

Model fallback didn't work out of the box. Had some hacking to get this to work. I _may_ have just misconfigured things. Always a possibility. 

According to `Claude`, this is a three layer system.

1. **Model config** in `openclaw.json` (which model + fallbacks)
2. **Auth profile cooldowns** (exponential backoff per provider)
3. **Gateway-level rate limiting** (10 attempts/minute, 5-minute lockout)

Actually still failing me today as I tried to set up a research and aggregate type morning brief related to my technical interests and it failed to run.

Still much to do to figure this out. The two dimensions of (a) rate limits and (b) token budgets was an unexpected hurdle.

### Lesson #3: Hardening

This one matters more than I initially thought. There's been a [lot of security scrutiny](https://fortune.com/2026/02/12/openclaw-ai-agents-security-risks-beware/) around OpenClaw recently: exposed instances leaking API keys, malicious skills on ClawHub, etc. Beyond the basics (fresh/wiped machine, limit access to credentials, don't expose to the public internet), I set my Claude Code agents on hardening the Docker setup: `cap_drop: ALL`, `read_only: true`, `no-new-privileges`, `non-root user`, `gateway bound to 127.0.0.1 only`.

Not a security expert, but _yet-another-reminder_ to take this stuff seriously is never a bad thing.

### Lesson #4: Cron!

Still a ways to go here, but I set up, among others, a couple useful columns in my Trello setup: `Today` and `Tomorrow`. Every morning my OpenClaw assistant will merge these and then send me an update on what to do today. Not 100% sure yet this message is useful, but automated board management is something that feels very appealing and worth exploring more fully.

***

Will follow up here after more experimentation, especially if this is something I continue to tune and it works its way into being a critical process for keeping myself organized.

Stay tuned!

