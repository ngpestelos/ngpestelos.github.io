---
layout: post
title: Testing Scheduled Jobs in Rails, Part 1
---

For the past two weeks, we were fixing a set of bugs related to recurring [jobs](http://edgeguides.rubyonrails.org/active_job_basics.html) in a Rails 4.2 app. These jobs typically send notifications to end-users to remind them of deadlines due within a few days. I wrote this article as a summary of what we did and what we could've done differently.

The main design improvement was to separate the notifications' preparation from the task of sending out these notifications. This enhancement would give us more control on how to send out notifications (e.g. in batches).

With this feature's initial release, it took use a few more deployments to get the jobs to behave as we liked. A [rake task](http://jasonseifer.com/2010/04/06/rake-tutorial) is responsible for running these notification jobs and in order to keep the rake task short, we adopted a convention around the jobs' class names. As we were quickly working to ship this feature, we forgot to rename some jobs, thus the rake task complained about missing classes. Another mistake we made was not seeing typos in our job classes sooner. These types of mistakes could've have been avoided had there been tests for the jobs before deploying.

The jobs are scheduled in [cron](https://en.wikipedia.org/wiki/Cron) and we found out that the [crontab](http://www.adminschoice.com/crontab-quick-reference) file must have a newline at the end. It also would have saved us more time during this initial release if we knew the crontab syntax deeply (and not having to reach out to Google every time).

I wish I could say this was a quick iteration, but we spent the following week fixing not-so-obvious bugs (see Part 2).