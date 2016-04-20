---
layout: dark-post
title: IronKey and Linux
date: 2013-04-25 10:58:33.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- centos
- ironkey
- linux
---
I utilize an ironkey for the office and at some point in time I had to utilize a headless linux box for file transfers.  However the linux box was missing some packages that are required to be unlock the iron key.  In this case I'm utilizing a CentOS 6 x64 system.  Here's some additional packages that I needed in order to get the ironkey working appropriately:

```
glibc-2.12-1.107.el6.i686
libgcc-4.4.7-3.el6.i686
```

Hopefully that helps some people out there.

### Resources:

http://www.howtodoityourself.org/2012/05/04/how-to-fix-libld-linux-so-2-bad-elf-interpreter-no-such-file-or-directory.html

### And the long story:

My error that I encountered:

```
[root@testcentos6 linux]# ./ironkey
-bash: ./ironkey: /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory

```

To troubleshoot:

```
[root@testcentos6 linux]# yum provides ld-linux.so.2
Loaded plugins: fastestmirror, refresh-packagekit, security
Loading mirror speeds from cached hostfile
 * base: centos.mirror.constant.com
 * centosplus: centos.mirror.nac.net
 * contrib: mirror.teklinks.com
 * extras: centos.mirror.constant.com
 * updates: centos.mirror.nac.net
glibc-2.12-1.107.el6.i686 : The GNU libc libraries
Repo        : base
Matched from:
Other       : ld-linux.so.2
```

Then I had another issue:

```
[root@testcentos6 linux]# ./ironkey
./ironkey: error while loading shared libraries: libgcc_s.so.1: cannot open shared object file: No such file or directory
```

So take two in troubleshooting:

```
[root@testcentos6 linux]# yum provides libgcc_s.so.1
Loaded plugins: fastestmirror, refresh-packagekit, security
Loading mirror speeds from cached hostfile
 * base: centos.mirror.constant.com
 * centosplus: centos.mirror.nac.net
 * contrib: mirror.teklinks.com
 * extras: centos.mirror.constant.com
 * updates: centos.mirror.nac.net
libgcc-4.4.7-3.el6.i686 : GCC version 4.4 shared support library
Repo        : base
Matched from:
Other       : libgcc_s.so.1
```

Results!

```
[root@testcentos6 linux]# ./ironkey
Enter your IronKey password: ********
Unlock successful.
IronKey device names are cd: /dev/sr1 hdd: /dev/sda
```
