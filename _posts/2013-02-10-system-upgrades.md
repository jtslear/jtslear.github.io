---
layout: dark-post
title: System Upgrades
date: 2013-02-10 23:44:14.000000000 -07:00
type: post
published: true
status: publish
categories: []
tags: []
---
So let me share with you a little story about upgrading application systems.

Where I work there is this server that runs this seemingly antiquated software, however, tis highly robust and is very actively maintained.  This system is designed to process insurance claims.  This system is extremely complex due to the large variety of variables for which claims are processed and amount of data needed to comb through in order to validate the smallest change.

Every once an while we get upgrades to this thing.  Just like any system testing is highly required.  We utilize a test system for which we copy the data, install the enhancement, and tell the team that performs the audit to have at it. One would assume it's cut and dry.  Here's why it took four tries for us to be successful:

Deployment One.  It was met with utter disaster.  The users testing the system found that it was not performing in any way like the test system was performing.  The enhancement provided was not operating as expected and it was blatantly obvious something was different between our test and production version of our app server.  After reverting back, we then continued to have issues later that week.  It was later discovered that the instructions provided by the enhancement were missing steps for which were rather crucial for ensuring the system would operate.  These steps may have been done undocumented  when the test system was being utilized  So the proper step was added, and the deployment to live was rescheduled

Deployment Two.  It was met with utter disaster.  The users testing the system found that it was performing, kind of.  As they were auditing the claims they found out that some claims were not being processed per the enhancement.  Due to the lack of knowledge for how many claims this would effect if left in the system for an extended period, it was deemed to roll back the live system.  Another thing to note, the test system was performing flawlessly.  In this case we went to the vendor with results from the audit and later to have the deployment reissued.  After another round of testing, deployment three.

Deployment Three.  It was met with utter disaster.  The users testing the system found that it was processing the claims that were being audited perfectly fine.  Awesome!  Past experience has taught us that we need to perform a full sweep of testing.  Those other tests were failing.  Added bonus  our test system was performing with the exact same results.  With the help of the vendor we were able to provide them extra details but unable to solve the problem before the end of the maintenance window.  We were forced to roll back, yet again.  It was decided that our method of copying data from our live and test system was flawed.  We would copy the data, and then test from that point forth with only specific data files.  This created a scenario for which the systems were not necessarily in-sync.  So each and every day, including the day of the deployment, we would copy the data over and put in the enhancement before testing.  This proved in the past that our testing was flawed when moving the deployment into place.  After more rounds of testing and another reissue of the deployment, number four.

Deployment Four.  It was met with success.  So much success in fact, that not only did our department perfect the documentation and the steps of the deployment, so did the people doing the auditing.  Things were able to be completed in much faster time, with greater attention to the proper details and in general things went very smooth.

Sometimes working in IT can be sucky.  In this case, 4 weekends were destroyed due to this ONE enhancement.  But in the future, with the improved documentation and testing strategies, we can all make better decisions and better plan for future implementations.  Collaboration is also very important.  Only teamwork and close communication with the vendor led us to be successful

Cheers for the next enhancement.
