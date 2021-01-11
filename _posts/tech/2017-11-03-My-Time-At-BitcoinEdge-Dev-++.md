---
layout: post
title:  "Bitcoin Edge Dev ++: My Experience"
date:   2017-11-03 19:19:21 +0700
tech: true
---

# Bitcoin Edge Dev++: An Incredible Experience and How I Got Here

![](https://steemitimages.com/DQmbJ82DF3E2F4fZAoTsv7dhMeQi8Xbij97KmPprMjZNbs2/Screen%20Shot%202017-11-04%20at%2010.04.24%20AM.png)

I had an absolutely incredible last two days learning the high level technical overview of Bitcoin. As I've had more and more people lately ask me about how to begin in this space, I wanted to begin this post with a quick recap on how I got to where I am.

To organize this post I am going to split it up into 5 parts:

1. My Journey into the World of Blockchain
2. Bitcoin Edge Dev ++ An Overview
3. Day 1
4. Day 2
5. Now What?

## [PART 1] My Journey into the World of Blockchain

![](https://steemitimages.com/p/2dk2RRM2dZ8gbkvz1yNBypLpwUHBnasMpK1zSCzdC5LcFTzDzEuGvrHLGYwnJPoNzPo1gxi8bTBcmn6L3k1Wc6ghRTXNJERAo2t4ZcLNxuwgBDdLmvXa75dbaq5VHHDNj6UQPh6HfM7NwdkMw4DR1dmbg98vDRJMkFSCcLEFTt?format=match&mode=fit&width=640)

My journey into the blockchain world started about 11 months ago midway through December of 2016; thus I am in the weird "new" by some people's standards and "old" by other people's standards position. A local tech consulting company, Collaborative Consulting (now CGI), put on an internship competition, a case competition challenging students to come up with the best combination of blockchain and college transcripts. Interested in winning the competition and securing an internship position, I opted for an independent study during my January term to focus on learning about blockchain technology. What started as a desire to win quickly transformed into a desire to learn. I found myself engrossed in white papers and side learning tangents, obsessed with understanding every detail about this exciting technology.

Since then, my blockchain obsession has taken me many incredible places:

* Presented about blockchain technology at CGI's inaugural East Maine Tech Night
* Published a Cryptocurrency Ticker App to the Pebble App Store
* Attended the MIT Bitcoin Expo
* Became a daily Steemit user and even a witness for a week
* Played around with Ethereum smart contracts
* Found a few local cryptocurrency enthusiasts and helped start a local meetup
* Attended MANY cryptocurreny/blockchain meetups in SF
* Attended Silicon Valley Tech Week
* Attended Tech Crunch Disrupt and got to ask Vitalik Buterin a question
* Helped Co-host a Steemit meetup with @ned as speaker (120+ people came!)

And now, I had the privilege of attending the inaugural Bitcoin Edge Dev++ 2 Day Technical Bootcamp. It is finally time to get serious about developing!

## [PART 2] Bitcoin Edge Dev ++ An Overview

As per the Bitcoin Edge website, here is the mission of the program:

*Bitcoin Edge Dev++ Tutorial is meant to focus on scaling the development capacity of the ecosystem via education of developers in the field of cryptocurrency and helping the industry streamline the process of developer training. The primary focus of this tutorial will be a basic first-principles introduction to cryptocurrency as well as cryptocurrency-specific engineering methodologies, security practices, and standard operating procedures.*

Consider a sports analogy for this situation:

The NFL has run out of talent. To solve this problem, they setup a two day bootcamp taught by top NFL coaches and players to educate and train the next level of talent and get them excited about playing pro football.

Pretty damn cool right?

Of course excited and hoping to maximize the situation, I prepped for most of the month leading up. My prep included:

* the first six chapters of http://www.learncpp.com/ in an effort to get a little bit of C++ knowledge under my belt
* Mastering Bitcoin, an incredible book for getting a high level technical understanding of Bitcoin (most of what I learned this weekend was actually covered wholly, or partially, in this book)
* Bitcoin Dev email list, Jimmy Song's newsletter, Reddit, and Twitter to stay up to date

Walking in the doors on Thursday morning, I was well prepared.

## [PART 3] Day 1

Both days of the two day event were held on Stanford's campus. Let me just say, WOW!!! Stanford's campus is absolutely beautiful. The palm trees, the adobe architecture, the modern buildings... just an incredible place. I showed up a few minutes early and got a good seat, front and center. We even received some swag (as I had secretly been hoping for).

![](https://steemitimages.com/DQmexcAKTFPjRPPszXgEU93XVAJvSW939nXR8SGUv9xDb6K/IMG_1358%20(1).JPG)

Here is the schedule for the day:

![](https://steemitimages.com/p/8DAuGnTQCLpunQuGfHnXTmxWbRQScCVGspXNWFwLneXTLQKj2KuLJcVy5VBMi36pCXgVzCTQyFTBCSqHK8G8K2mmxm4D5sghdBe4FyAHUGvgKJhprcp8ABTPXsiGi7vWsnSF9EANjqmLCo2Nm5dD2GcGmWCEUEwE9xriNMfD9kA?format=match&mode=fit&width=640)

While I certainly learned too much to explain here in detail, here is a brief synopsis of what I learned. We started with the most basic elements of any crypto system... cryptography. Jimmy Song's system went from elliptic curves to finite fields to a combination of the two, to ECDSA to asymmetric cryptography to digital signatures, to P2PKH addresses and signatures to P2SH addresses and signatures. While it may seem like a lot, it is in fact a very nice, logical progression. Luckily I had just taken a cryptography class last semester so, I was one of the least confused people in the room.

John Newbery, core developer and active contributor was up next. He explained the basics behind blocks, the blockchain, transactions, and the mempool. While at times it got technical and down to the byte (literally) it was fascinating to see how all these moving parts worked together to create the Bitcoin ecosystem.

With day 1 done, I had a pretty good overview of how the entire Bitcoin system worked; Day 1 was layer 1, focused on the fundamentals of the Bitcoin protocol. Day 2 was layer 2, things built on top of layer 1, ex) Lightning Network.

## [PART 4] Day 2

I found Day 2 to be slightly more interesting as it focused less on the fundamentals and more on applications (and even some code).

Here is the schedule for the day:

![](https://steemitimages.com/DQmZ2fy6xiV78Hzt4fWPmyiKm3HCcsgABMtJW7h3AeHTScp/Screen%20Shot%202017-11-04%20at%2010.25.49%20AM.png)

We began with the last of the fundamentals: HD wallets, mining, and common attacks. Most of this was knowledge I had coming into the day and was covered in Mastering Bitcoin.

However, the second part of the day was incredibly exciting!

RPC: RPC stands for remote procedure call and allows for people to connect and interact with their Bitcoin node remotely. I had always been wondering how this worked because in theory, if I wanted to create a Bitcoin application, it would require me to connect to a node in order to access the Bitcoin network. This session presented us with a basic application and explained the basics of interacting with and setting up full nodes.

Lightning Network: WOW the Lightning Network is awesome! For those of you who are unfamiliar, at its most basic level, the Lightning Network is a second layer protocol for Bitcoin. It works by rooting an initial multisig transaction on the blockchain and then setting up a payment channel between two people off-chain. Via a multi-hop architecture, you can then create a network effect between these channels and connect any two people, allowing for fast, private, cheap transactions that don't bloat the Bitcoin blockchain. Cool right? Following Tadge's explanation of the LN, we got play around with a live demo! I received fake bitcoins on the lightning network from the creator of it!!!!! What a great time!

Crosschain Swaps: Crosschain swaps allow you to privately and securing transact assets between two different blockchains. This presentation was followed by a simple demo. While exciting, the premise is sort of cryptocurrency laundering (at least from some people's perspectives). Very cool for some people, not so much for the government :)

## [PART 5] Now What?

I am by no means a Bitcoin expert, and am still years of experience away from being technically skilled enough to be a core developer. However, I am a pretty skilled full stack developer with a high level technical understanding of Bitcoin. So ultimately, I believe the best way for me to contribute to the ecosystem is to use the skills I have and the knowledge I gained and try to develop layer 2 applications. Here are some thoughts I have on where to go next and things I want to experiment with:

* Develop a front end facing Bitcoin application using my React/web dev skills
* Build a simple RPC application
* Develop a simple JS app to mimic Bitcoin's fundamentals
* Mess around with the Blockstream Bitcoin Satellite

Now that I am pretty knowledgable in the Bitcoin basics, I am more than happy to help anyone who may be struggling and needs a little bit of clarification! Like I said, I am not an expert by any means, but I undoubtedly know more this week than I did last week... and I believe that if I continue knowing a little bit more each week, at some point I'll be pretty good at this stuff.

Hope everyone has a wonderful Fall weekend! Special shoutout to all my fellow Steemians at SteemFest! Cheers and Steem on!