---
layout: post
title: Testing for HTTP 401 Errors in JMeter
date: 2013-08-28 20:48:14.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- jmeter
---

### UPDATE:
> Thanks for tip. However as per JMeter 2.11 it’s quite enough to use just one Response Assertion, erroneous HTTP status code is being ignored, but the assertion checks the response for any patterns defined and if some condition is not met the sampler is being marked as failed. See [How to Use JMeter Assertions in 3 Easy Steps](http://blazemeter.com/blog/how-use-jmeter-assertions-3-easy-steps) guide for more details.

I recently picked up jmeter and writing a test looking for 401 errors produced from the server.  In my particular case I wanted jmeter to recognize that as a specific error that is valid.  The problem is that by default any 4xx or 5xx error is noted as a problem right off the bat.  Due to this we need to a bit of extra work in order for jmeter to tell me that 401 is a valid response for my test.  To make this work, one needs to add _TWO_ Response Assertations.

The first Assertation you need to configure the 'Ignore Status' to true.  This might seem convoluted because you need to look for the 401, but we'll tackle that in the next Response Assertation.   We ignore the status because jmeter processes responses in a specific order, the first assertation would be ignored because jmeter sensed the error.  If we were to put the pattern of 401 in this one, it would never be tested for.

The second assertation is then configured to test the 'Response Code' containing a 401.  This assertation will look at the response code and return an OK status for this test like we expect.

Results from this test are exactly what I needed to prove the 401 from the server was being triggered appropriately.
