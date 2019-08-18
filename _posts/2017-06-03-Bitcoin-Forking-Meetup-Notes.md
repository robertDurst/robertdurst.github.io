---
layout: post
title:  "Bitcoin Core Devs Meetup: Forking Meetup Panel Notes"
date:   2017-06-03 19:19:21 +0700
---

**Original:** [link](https://steemit.com/bitcoin/@robertdurst10/my-notes-from-the-bitcoin-forking-panel-meetup-silicon-valley-what-an-incredible-event-and-the-future-of-bitcoin-is-bright)

# Bitcoin Forking Panel Meetup @ Silicon Valley

![](https://steemitimages.com/DQmTLJPW4WMgGmXMr3WgrBnSqc8X6kq1YR1hEwhFdFNpAC7/IMG_1268.JPG)

## Overview

Today, @sahil.hingorani and I attended the San Fransisco Bitcoin Meetup's Forking Panel discussion in the heart of San Fransisco. The meetup consisted of a strong panel of speakers who discussed the current state of Bitcoin, including the recent forking proposals, Segwit, and Bitcoin Cash. As someone really interested in this space, I took lengthy notes on my computer during the panel discussion. This post is essentially a concise version of the notes I took. Enjoy!

On the Panel

Here are the panelists from left to right.

Eric Martindale: left Blockstream to devote more time to building Fabric, an experimental peer-to-peer information market.

Elizabeth Stark: Cofounder at Lightning Labs and fellow at Coin Center.

Christopher "JJ" Jeffrey: CTO of Purse.io and creator of BCoin, an advanced fullnode implementation of bitcoin.

Laolu "roasbeef" Osuntokun: Cofounder at Lightning Labs and a btcd contributor.

Some Background Information - Relevant Terms

### FYI 1: Hard-fork vs. Soft-fork

Currently, there are two acronyms floating around that confuse a lot of people: UAHF and UASF.

UASF stands for User Activated Soft Fork. UAHF stands for User Activated Hard Fork. I will get to what these two solutions seek to do, but before I go there, what exactly are HF's and SF's?

Hard Fork: a hard fork is a splitting of the blockchain, resulting in two coins. It essentially creates two coins, two blockchains, and two networks that originated from the same blockchain. An example is Ethereum and Ether Classic. Both started as Ethereum and both can trace their origination to the same blockchain, but now the two are completely different entities.

The UAHF that people are talking about will create Bitcoin Cash and is proposed by Bitmain. This is their plan for opposition against UASF and BIP148.

Soft Fork: a soft fork is a backward compatible upgrade that make minor changes to the network. An example of a soft fork is the P2SH protocol that allowed for new Bitcoin transaction types. The difference between a soft fork and a hard fork is that a soft fork only needs majority rule and is compatible with nodes that have not participated. Hard forks are not compatible with non-participating nodes and thus cause disruption and confusion within the system.

BIP148 is a USAF.

### FYI 2: Segwit

Segwit stands for segregated witness. This allows for certain parts of transaction data to be left out of transaction blocks, and thus more transactions can fit in a single block.

### FYI 3: BIP91

Allows for Segwit2x (Segwit + 2mb increased block size) to be activated if 80% of the miners signal acceptance, versus the previously required 95%.

### FYI 4: BIP148

A UASF that seeks to enforce all Segwit ready nodes to run Segwit. Segwit was accepted into the system by miners in a MASF (miner activated soft fork), aka BIP91.

### FYI 5: Lightning Network

The lightning network is the second layer of the Bitcoin protocol. What does this mean? Before yesterday I had no idea.

Basically, the lightning network is a separate entity that works on top of the blockchain. What that means is that an application can do some really cool things that the blockchain may not be able to handle while functioning on the lightning network. Then, later the lightning network can finalize these actions on the blockchain.

An example is reversible transactions. On the blockchain, this is not possible. But, with a second layer protocol like the lightning network, reversible Bitcoin transactions may become a reality.

### FYI 6: Bitcoin Cash

Some people (even some who originally backed Segwit2X) are unhappy with the direction of Bitcoin and are creating their own currency. While this carries the Bitcoin name, it is an altcoin like Ethereum, Litecoin, etc. Bitcoin Cash is a fork of Bitcoin with no Segwit and 8mb block sizes instead of 2mb. If you act before August 1, ViaBTC will freeze your BTC in exchange for Bitcoin Cash. Many people from the meetup believe this is a scam.

## The Panel Discussion

### Question 1: What is the Problem?

a) Bitcoin's network is reaching its limit and cannot keep up with the number of transactions on the system.
Bitcoin is a scarce resource, meaning that with more demand and a limited supply, prices will rise. Unfortunately, with a fixed transaction size, fee's must increase more and more as people essentially bid to get their transaction included on the blockchain. Thus, micro (small) payments become obsolete, going against the original intentions of Bitcoin visionary, Satoshi Nakamoto.

Solutions to this issue are to make transactions smaller and more efficient (Segwit) and to make the block sizes bigger (Bitcoin Cash).

b) Issue with hard forks.
Bitcoin Cash proponents seek a hard fork. Nearly all the panelists agreed that a hard fork is not a desirable solution. This would create confusion between the two competing blockchains and create unhealthy vulnerabilities that would ruin the reputation of Bitcoin as a whole. One of the panelists even went so far as to say a contentious hard fork may seriously damage and even destroy Bitcoin.

c) Who governs Bitcoin?
During the discussion of why hard forks are not desirable, the question of governance came up. The golden question here is who governs Bitcoin, the miners or the coders? The obvious argument seemed to be the miners, since they maintain the system. However, these miners are incentivized to protect their investments and so their interest should be aligned with those of the ecosystem. Thus, if they are making moves that instill distrust in the average Bitcoin user, they would likely alter their actions to regain the trust of the average user in fear of damaging the value of their bitcoins.

### Question 2: Why didn't Segwit2X go smoothly?

For those on the panel who support Segwit, Segwit2X signaling means it is time to celebrate. Sure, they had to make some concessions and increase the block size to 2mb, but the Segwit supporters argued that passing Segwit far outweighed the negative effects that may come with increased block size. However, there are still those that are very opposed to Segwit. These people are supporters of UAHF and are proponents of Bitcoin Cash. So, even though it seemed like a half-way point was met, there are still miners and large organizations making plans to split Bitcoin because they are unsatisfied with portions of the agreement.

### Question 3: What is your vision of Bitcoin? Is Bitcoin a currency or a stored value?

Here is the unabridged version of my notes for this question :)

Laolu (playing devils advocate in support of Bitcoin Cash): it is a currency, not a stored value (DEFEAT ENTIRE PURPOSE if not used as a currency) NEED more people on the system, MORE supply, less fees, LET MARKET DECIDE BLOCK SIZE.

JJ: into currency and value store BIG ADVOCATE OF LIGHTNING, lightning debate between idealism and pragmatism, SEGWIT2X just good because we got segwit and progress is the MOST IMPORTANT and put before the privacy issues.

Elizabeth: believe it is both currency and stored value, lightning allow for smaller transactions, lightning allows for local consensus and then the bigger network can confirm the rest, NOT EITHER OR SCENARIO.

Martindale: blockchain bad for payments, credit cards way faster, struggle to understand urgency to grow network because
SATOSHI vision is not met, need to do this first because use case is not met, bitcoin is a trust anchor and a reference point for all, multiple implementations good for the long run, bitcoin protocol detailed and complex, many edge cases exist.

### Question 4: What is happening in November?

Second half of Segwit kicks in and people think 3rd coin maybe as mining pools may go 50/50. Basically, even though there was lots of optimism coming from the panel, we are by no means out of the woods yet.

### Question 5: How do we come together as a community and what can we do better?

1. We need to NOT duplicate work and effectively communicate better as a community
2. Make lightning more user and developer friendly (basically everyone there was a HUGE lightning supporter)
3. Party, pop some bottles, and celebrate Segwit
4. Scaling and developing beyond the blockchain, aka layer one (and not just lightning)

### Random Tangent 1: Segwit and Scripting?

Segwit allows for more script versioning meaning more complex codes can be put on the Bitcoin protocol. Some of these include:

* Complex contracts (vault, covenant)
* Time locks (smart contracts)
* Witness programs (GO STEEM!!!!)

Segwit also allows for new contracting languages and the implementation of sidechains. Sidechains are huge because with sidechains and script versioning you can do any change you want without a hard fork.

At this point in the discussion, the panel went into a description of the lightning network. Here it got a little technical. The lightning network is not Turning complete, meaning it basically cannot loop. The people on the panel viewed this as a good thing. Basically, the computational challenge with turning complete systems (the halting problem) is you CANNOT REASON the expense of a program because you cannot predict the outcome. Thus you have problems such as those seen on Ethereum (Ethereum supports Turning complete languages).

### Random Tangent 2: Why Ethereum sucks...

Inevitably, the time came to Shit on Ethereum. Basically, the panel concluded that Ethereum is way worse with its scaling issues. Also Ethereym has very few full nodes validating the network and at the end of 2018, it will be at 1TB!

Post Discussion Questions

There were many, but these are the best ones.

As developer, how can I help ecosystem?

1. Help get better documentation out there
2. Lead outreach events
3. Recruit more developers
4. Once Bitcoin evolves into more layers, other can get involved without dealing with blockchain
5. Help develop LAPPS - lightning network apps
6. Find an open source mentor on a project you are building; seek individual who can guide you to find a place to contribute

Why is Bitcoin the best?

According to the panel, Bitcoin is the best because:

1. It has the best developers
2. It is the most scalable
3. It has established the desired network effect
4. It is about to really explode with all this extra utility
5. It has the best memes

## Conclusion

As someone biased towards DPOS (Delegated Proof of Stake - think Steem, Peerplays, EOS, Bitshares) systems, I am a little weary of Bitcoin. However, the optimistic vibe radiating from the panel and throughout the room of 150 people was contagious. It is hard not to be excited about the future of Bitcoin and the future of cryptocurrency as a whole. I still believe there is a second mover advantage in this space, and the coin that is able to solve Bitcoin's problems AND find a killer application for this technology will ultimately be the most successful. No matter how optimistic people seem to be, I am still not convinced that a platform that has grown so large can really make the changes it needs without disrupting a majority of its users.

Either way, this was an incredible experience, and I am itching to see how it will all play out. Even though I am not currently invested in Bitcoin, I am interested in the tech debates and curious to see how this will effect the many, many altcoins... and now that ICO tokens are illegal securities, EVERYTHING just got more interesting.

Anyway, I hope this helps some people! I will begin attending these meetups more regularly this Fall since I will be living in San Fransisco.

Cheers and Steem on!