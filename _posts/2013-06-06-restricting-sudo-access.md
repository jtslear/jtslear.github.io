---
layout: post
title: Restricting Sudo Access
date: 2013-06-06 21:45:42.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- linkedin
- linux
- visudo
---
On a properly secured system, sudo access should never be provided.  Even if it is provided, one should avoid letting loose on the access.  It's best practice to only enable specific commands that enable the end user to complete their jobs.  However, some battles cannot be won.  So, let's discuss removing access to certain items.  We'll do this using visudo.

Execute this command to get started:

```
visudo
```

The first thing we should do is specify a log where so we can quickly delineate the actions being taken by sudoers

```
Defaults logfile=/var/log/sudo.log
```

We then want to force sudoers into a headache and make sure they type in their password every single time.

```
Defaults timestamp_timeout=0
```

Now we want to make things easier on the eyes and create aliases so our later command is easier to look at quickly

```
Cmnd_Alias NVSU = /usr/sbin/visudo
Cmnd_Alias NSU = /bin/su
Cmnd_Alias NSHELLS = /bin/sh,/bin/bash
Cmnd_Alias NYUM = /usr/bin/yum
Cmnd_Alias NPASSWD = /usr/bin/passwd
```

And finally, we enforce our settings on the appropriate group of users that have sudo access:

```
%group_name ALL=(ALL) ALL, !NVSU, !NSU, !NSHELLS, !NYUM, !NPASSWD
```

The above line indicates that whomever is part of the group_name, can execute everything, as any user, and run all commands EXCEPT running visudo, su, sh, bash, passwd, and yum. We prevent the following from happening:

* use of visudo to make modifications to the sudoers file
* switching to a different user
* executing a new shell as root
* installing packages and updating the system
* changing the root password on the system

I know there is some more that should be added to this list.  However, for my particular application, this was suitable.

### Warning:
visudo is very powerful and if not done properly it can break sudo or provide unintentional access.  Do the research before you modify this configuration!

### Resources:

* [sudo...sudoers](http://www.sudo.ws/sudoers.man.html)
* [serverfault...how-to-disable-sudoer-from-editing-etc-sudoers-file](http://serverfault.com/questions/308198/how-to-disable-sudoer-from-editing-etc-sudoers-file)
* [nixcraft...sudo-exclude-commands-disable-sudo-su-bash-shell](http://nixcraft.com/networking-firewalls-security/15132-sudo-exclude-commands-disable-sudo-su-bash-shell.html)

### Side Bar:
A sudoer still has the capability to compile or rpm a different shell.  From which gets around this entire setup.  Darn.

The above is for rpm based systems, Red Hat or CentOS.  Modify accordingly for debian type OS's.
