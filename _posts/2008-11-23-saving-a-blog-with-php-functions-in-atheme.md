---
layout: dark-post
title: Saving a blog with php Functions in aTheme
date: 2008-11-23 19:38:41.000000000 -07:00
type: post
published: true
status: publish
categories: []
tags: []
---
So I ran into this awesome situation. After I have moved and got my server up here with me, I am faced with a temporary ordeal.  I have no control over the router and how information is relayed to my server.  Nor do I have control over the IP addy in which I am given.  It isn't my networking equipment.  So I was forced to change the address, and until I can get any sort of port forwarding or DMZ set up, I'm without access to my domain from my server.  So as of the writing of this post I've had to figure out a way to update wordpress' url and home link settings without being able to login to the admin pages.

Anytime you try to go to the site I am forced to be redirected to the domain, however at the time of this writing that ends up hitting the router that I'm behind, which doesn't point to this server obviously, therefore I'm thrown a 404 error.  The only way to allievate this situation is to change the URL's to point to the local IP. At least that is the only thing I can think of as of right now.

How can you do that if you can't even get to the admin pages?  It's almost too easy.

Inserting some php functions in the active theme is all it takes.? Here are those functions.

```
update_option('siteurl','http://example.com/blog');
update_option('home','http://example.com/blog');
```

These functions needs to be placed directly after the `<?php` line in the functions.php file of the active theme.

You can discover how I did this from [this post here](http://codex.wordpress.org/Changing_The_Site_URL).  There are other methods available.  That post tells you how to do it.
