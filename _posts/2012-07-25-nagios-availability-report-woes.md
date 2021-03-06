---
layout: dark-post
title: Nagios Availability Report Woes
date: 2012-07-25 01:12:48.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- nagios
---
I've recently been tasked with providing reports on a regular basis that show the status of our servers and services that we monitor using nagios. We've devoted a rather large amount of time to providing nagios with many checks and graphing of as much data as possible, and making custom plugin's wherever we see fit. Nagios has been fantastic thus far with it's flexibility and ease to customize.  Though daughnting for the new commer, nagios has proven to be a very valid candidate for enterprise worthy monitoring.  That is, until I discovered an interesting problem that I cannot find a resolution to.

Lets roll through this by looking at a standard host check:

![host_check_current]({{ site.url }}/assets/hostcheckcurrent.png)

As you can see from this screenshot the host is clearly up.  In fact, the last status change was 10:17AM on the 28th (planned server reboot).  With this we can devise that as of right now the server is up with a status of OK.  So lets run an availability report.

![host_availability_report]({{ site.baseurl }}/assets/hostavailabilityreport24hrs.png)

As you can see from this report, the server appeared to go down on the 25th.  There are no entries of the server having gone down on the 28th due to a reboot.  There are no entries stating the server came backup on the 28th.

With this information we can derive two things.  One, something is wrong with nagios somewhere.  Whether it is our specific configuration or nagios was lagging and missed some data, somewhere he didn't log the fact that the server has cycled a couple of times at all.  Two, there is a disconnect between how nagios stores the data that shows that a server is online and how nagios pulls the data to provide us the reporting.  I would expect the second to be true regardless due to the method of capturing this data.  The problem I have with this, is that despite the fact that the server is OK RIGHT NOW, nagios's availability report proves otherwise.  This is causing a rather large negative impact on our reports.

Note that we do have a parenting structure in place so we decided to create a mock setup with another server with one parent.  We fakingly brought down the fake parent, followed by the fake server, to fully recreate what happens during such an outage.  The systems were brought back up at the same time.  Nagios reported all systems coming online just fine.  Our availability report looks as it should.  But when proceeding to this fix for other hosts, it didn't help out the issue.

In our case we had stopped the service, removed the performance data that the reports utilize and restarted nagios.  We kept our graphs, but nagios will simply no longer have the capability to report after this period of time.  A bummer, but I want my reports to be correct for the time being.  I'll be looking into this deeper in the future.
