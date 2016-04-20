---
layout: dark-post
title: Another Redo
date: 2009-03-01 18:52:40.000000000 -07:00
type: post
published: true
status: publish
categories: []
tags: []
---
My site is back online!

And is in need of some serious updating.  My site went offline when I decided once again to move from using my laptop as a server, to putting an old white box back online as the new webserver.  The box is really old, at only 733MHz processor, and only 384MB of RAM.  However, it's only purpose is to server as a website for whoever wants to read it, a samba share for the home network, and to process Folding@Home.  Currently only the website part works and I'm continuing trying to tweak it a bit.  I've had a bit of trouble finding the best way to put my website on here.  Currently everything that is available to the public sits inside of folders inside of the /var/www directory.  I would rather have a user get to my blog using only the FQDN.  But right now users still need to add the /wp to the end of the FQDN.  I'd like to cut out that slight need for such.  Currently, for my sake, apache will allow directory browsing as I always forget that I need to add it there when I type in my website's address.  I don't use bookmarks.  I am modifying the site-available and sites-enabled configuration to find out which works the best and to determine which will not break other virtual sites that are being hosted on this server.

Currently my samba share isn't work either.  The proper ports are open, but authentication fails for some reason.

And I haven't set up Folding@Home yet either.  On a slow processor like this it will take more than 15+ days for one workload, but, might as well waste electricity.  This server is plugged in for one reason.

Until my next post/update, have a great weekend.
