---
layout: post
title: Microsoft SQL 2008 configuration authentication with BlogEngine
date: 2008-10-28 20:23:45.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags: []
---
So I wanted to post up here my issue I had when I first tried to install BlogEngine and forcing it to use a database instead of the default XML.  The reason I wanted to use a database in the first place is the fact that I have Microsoft SQL 2008 installed on this machine and nothing to really use with it.  So taking advantage of such products already available, I thought I'd go ahead and start it up.  Installation goes just as simple as the README's take anyone.  However when I was first trying to reach the brand new page, it was a no go.  I was thrown an error indicating that the user, in my case blogger, was not able to log in.  .NET fails to actually tell me exactly what the error was when trying to log in, it's just nice enough to provide me the numerous exception errors that kept coming as it proceeded to process whatever it does. 

Upon learning to figure this out, I scrambled through the log files to figure out why I couldn't log in.  Everything was set up correctly I know that for a fact.  But going through the log errors that SQL throws out, it was discovered that apparently you are not able to log in when SQL is configured to use Windows Authentication.  During the install of SQL Server, I chose that as a method of logging into the database as it had not occured to me as to how much a difference it would make to use the database system.  I thought at first this error was null as I did end up creating a user named blogger on the system.  But obviously it did not work properly. 

To alleviate the poor situation I was in, I simply reconfigured Microsoft SQL to use Windows Authentication alongside SQL User Authentication.  After recreating a user just on my behalf to make sure I was doing everything correctly, I was rolling.  Of which can be verified as the post created in the database is right below this post. Yay for fixing problems!

Happy Blogging.
