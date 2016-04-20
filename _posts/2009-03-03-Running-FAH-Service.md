---
layout: post
title: Running Folding@Home as a Service
date: 2009-03-03 18:31:55.000000000 -07:00
type: post
published: true
status: publish
categories: []
tags: []
---
So a google search on running Folding@Home as a service turns up a crap ton of results.  I'm here to add to that list :-)

You should go ahead and

```
sudo su -

```

The first thing to do obviously is to download the latest and greatest from Folding@Home, at the time of this post:

```
wget http://www.stanford.edu/group/pandegroup/folding/release/FAH6.02-Linux.tgz

```

Untar

```
tar xvvf FAH6.02-Linux.tgz

```

Move the files to where they "need to go"/"out of the way"

```
cp -R FAH6.02-Linux/* /usr/share/folding

```

Let's go to that directory so that we can run the initial configuration, after the questions are done and it begins to start processing workloads just Ctl+C

```
cd /usr/share/folding

./fah6

```

After closing stopping the script we will create a startup script.  So let's go home.

```
cd ~

```

Create the new script

```
vim fah6

```

Copy the following into the file then :wq

```
#!/bin/bash

cd /usr/share/folding

./fah6 >> folding.log 2>&1 &

```

Then we add the script to the /etc/init.d

```
chkconfig -a fah6

```

Bangin'  You are now done.  To verify it works:

```
/etc/init.d/fah6

```

Wait a couple seconds for Folding@Home to start then run

```
top

```

to see it slam out your CPU.

If you'd like to see it's progress here and there observe the log file that is created within the /usr/share/folding directory.  I would like to see if this can be changed/moved so that I can do some stuff with the log file.  Particularly have the ability to be able to show off how far my computer gets with each work unit.  That shall be saved for a later date.
