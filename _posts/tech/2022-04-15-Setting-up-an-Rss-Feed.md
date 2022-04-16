---
layout: post  
title:  "RSS Feed Up and Running! How I did it."
date:   2022-04-16 00:00:00 +0700   
tech: true
---

Finally set up RSS feed so my followers can actually subscribe!

![hit that subscribe button](https://media.giphy.com/media/73oSygWJFG9K1ha75z/giphy.gif)

Turns out it's quite easy to set up an RSS feed with [Jekyll](https://jekyllrb.com/). It's spelled out fairly well [here](https://www.r-bloggers.com/2019/12/creating-an-rss-feed-to-add-your-jekyll-github-pages-blog-to-r-bloggers/), although it mostly focuses on getting set up with R Blogger. I will be paraphrasizing and focusing on a general RSS feed setup.

**[First]**, set up your Jekyll blog. Add the gem `jekyll-feed`.

Gemfile
```
*** abbreviated ***

gem 'jekyll-feed'

*** abbreviated ***
```

_config.yml
```
*** abbreviated ***

plugins:
  - jekyll-feed

*** abbreviated ***
```

**[Second]** run `bundle install` to install locally. My local ruby env. is kinda messed up so I skipped this step. Test in prod right?!?

**[Third]** push to GitHub. Once it is pushed check that all is well by navigating to `http://YOUR_PAGE/feed.xml`

At first, if you do not have an RSS reader it may say something along the lines of `No RSS reader installed.`

**[Fourth]** install an RSS reader if you don't have one.

As an example, let's say you downloaded the [`RSS Feed Reader` Chrome extension](https://chrome.google.com/webstore/detail/rss-feed-reader/pnjaodmkngahhkoihejjehlcdlnohgmp?hl=en) and navigate to [my blog's RSS feed](https://robdurst.com/feed.xml), you should see something like this:

![my blog as seen by RSS reader](https://imgur.com/TVkFevR.png)

**[Fifth]** users should be able to subscribe to email alerts. While you could use the above Chrome extension, I am using [FeedRabbit](feedrabbit.com). Not 100% sure how well it works because I believe it emails notifications daily (and I just set this up today), but I imagine it gets the job done.
