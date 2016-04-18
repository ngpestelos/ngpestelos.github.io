---
layout: post
title: Testing Scheduled Jobs in Rails, Part 2
---

In my previous post, I briefly mentioned the importance of having tests for the job classes (we still left the rake tasks without tests). We started the week of April 11 by finishing what was left of the renaming of the job classes.

We noticed that the notifications did not go out as scheduled. The jobs typically query the database for people to notify for a particular date range and these jobs use a lot Arel scopes. However, we noticed in two different jobs, the usage of a particular scope was different. One job takes the present day as its range while another job takes plus-or-minus two days. Investigating this problem involved printing out the generated SQL statements and to see if these scopes still make sense.

We also found one Arel scope uses its own hard-coded date range (it was perhaps a good idea at the time) and this affected our parameterized date range, casting more doubts on the reliability Arel scopes. One slippery part of Arel though is that it is easy to convince oneself that it works (for simple queries), but combining scopes with a (through `merge`) is a different matter.

To regain confidence, we re-wrote our tests to exercise these scopes. We used [ActiveSupport::Testing::TimeHelpers](http://api.rubyonrails.org/classes/ActiveSupport/Testing/TimeHelpers.html) to fix the system's time during test (similar to what [TimeCop](https://github.com/travisjeffery/timecop) does). With this ability to jump in time, we could write our fixture data without having to worry about settings. Our tests for the scope usually involves checking if a particular fixture data is included in the scope or not.

Part 3.