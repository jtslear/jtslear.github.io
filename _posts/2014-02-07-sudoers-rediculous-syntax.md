---
layout: post
title: Sudoers Rediculous Syntax
date: 2014-02-07 19:54:44.000000000 -07:00
type: post
published: true
status: publish
categories: []
tags:
- linux
- sudo
- visudo
---
I came across something that threw me for a loop today.  I needed to modify sudoers so that I can help secure a service account to execute a script as a different user.  So here's my config that was dropped in /etc/sudoers.d/somefile

```
Cmd_Alias THISCOMMAND = /path/to/the/script
serviceaccount ALL = (otheruser) NOPASSWD: THISCOMMAND
Defaults!THISCOMMAND !requiretty
```

Nothing out of the ordinary, but when validating the command, sudo fails with an error message:

```
[root@hostname ~]# sudo -l -U serviceaccount
sudo: >>> /etc/sudoers.d/somefile: syntax error near line 3 <<< 
sudo: parse error in /etc/sudoers.d/somefile near line 3
sudo: no valid sudoers sources found, quitting
sudo: unable to initialize policy plugin
```

Checking and rechecking the man page and sudo documentation lead me no where.  I even moved this config information into visudo, which worked fine.  So I KNOW my syntax is correct.  Long story short, _sudo is expecting a new line in the file_.  After throwing a new line in the config, all is well.  Here's the diff:

```
@@ -1,3 +1,3 @@
 Cmnd_Alias THISCOMMAND = /path/to/the/script
serviceaccount ALL = (otheruser) NOPASSWD: THISCOMMAND
-Defaults!THISCOMMAND !requiretty
\ No newline at end of file
+Defaults!THISCOMMAND !requiretty
```

After that, validating sudo returned what I expected it too.

I'm not the only person that has seen this either:

* [groups.google.com...puppet-users/nGKutJ9Iw2E](https://groups.google.com/forum/#!topic/puppet-users/nGKutJ9Iw2E)
