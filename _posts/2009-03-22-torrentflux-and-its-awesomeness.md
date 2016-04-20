---
layout: post
title: Torrentflux and it's awesomeness
date: 2009-03-22 18:48:36.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags: []
---
TorrentFlux is possibily easier to install than a torrent client on any users computer. Seriously!

```
apt-get install torrentflux
```

Within 45 seconds torrentflux will have installed, configured, and restarted apache.  Simply browse to:

```
http://localhost/torrentflux
```

after the install and POOF.  Finish the configuration.  Unless something didn't install properly, everything is well more than dandy.  In my case I changed the default location in which all torrents are saved due to the strangeness of the hard drive setup in my system.  By default torrentflux is set up to download to the `/var/some/directory`. I have it download everything in the `/home/something directory`.  The very first option on the admin page will allow you to choose a different directory.  The only mandatory thing is to make sure that directory has the `777` permission.

```
chmod 777 /directory/name/goes/here
```

The default directory has already been setup like this.

TorrentFlux uses bittornado by default, rolling out perl for use, it starts a process for each torrent that is to be downloaded. That process obviously stays up until the torrent is to be cleared from the queue.  That queue includes whether the torrent is being downloaded or is seeding.

Probably the only bummer of doing torrent downloads this way, is that it isn't on the computer in which I use the media.  However, it helps me out when I move files around, because all of my media stays on the server, and is just streamed through the network whenever I want to take advantage of viewing said stuff.  YAY TORRENTS!  Peace out peeps.
