---
layout: post
title: tmux compile failure
date: 2013-03-22 18:22:43.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- libevent
- linux
- tmux
---
I love me some tmux.

However sometime between CentOS 5 and CentOS6 something broke and tmux no longer compiles.  Here's my error message:

```
...
control.c: In function ‘control_callback’:
control.c:62: warning: implicit declaration of function ‘evbuffer_readln’
control.c:62: warning: nested extern declaration of ‘evbuffer_readln’
control.c:62: error: ‘EVBUFFER_EOL_LF’ undeclared (first use in this function)
control.c:62: error: (Each undeclared identifier is reported only once
control.c:62: error: for each function it appears in.)
control.c:71: warning: implicit declaration of function ‘time’
control.c:71: warning: nested extern declaration of ‘time’
make: *** [control.o] Error 1
[root@testcentos6 tmux-tmux-code]#
```

After some googling people refer others to utilize the latest version of libevent. Well, I wasn't using the latest I was using whatever comes with the CentOS repos. So I decided to fix this by removing those:

```
yum remove libevent libevent-devel libevent-headers
```

Then compile my own libevent. I don't do anything out of the ordinary on this server so I'm not even sure if anything I use depends on it:

```
wget https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz
tar xf libevent-2.0.21-stable.tar.gz
cd libevent-2.0.21-stable
```

Their README was very basic, so rock it:

```
./configure
make
make install
```

I then returned to my tmux source:

```
cd /usr/local/src/tmux-1.7
make clean
make
make install
```

Then another issue:

```
[root@testcentos6 tmux-1.7]# tmux
tmux: error while loading shared libraries: libevent-2.0.so.5: cannot open shared object file: No such file or directory
```

Luckily this error has been seen before. After another quick google:

```
ln -s /usr/local/lib/libevent-2.0.so.5 /usr/lib64/libevent-2.0.so.5
tmux
```

AWESOME!

Resources:

* [Tmux at SourceForge](https://tmux.github.io/)
* [libevent](http://libevent.org/)
* [libevent-2.0.so.5: cannot open shared object file: No such file or directory](http://www.nigeldunn.com/2011/12/11/libevent-2-0-so-5-cannot-open-shared-object-file-no-such-file-or-directory/)
