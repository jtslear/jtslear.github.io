---
layout: dark-post
title: VMware vSphere CLI 5.1 Install Failure
date: 2013-06-27 14:10:56.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- vmware-cli
---

Yet again, I'm speaking about something that I had fail to install on my workstation.

The error message:

```
http_proxy not set. please set environment variable 'http_proxy' e.g. export
http_proxy=http://myproxy.mydomain.com:0000 .
ftp_proxy not set. please set environment variable 'ftp_proxy' e.g. export
ftp_proxy=http://myproxy.mydomain.com:0000 .
```

Unfortunately I do not run a proxy in my environment. I'm not sure why they don't make this easier it insists on this, but it fails out. To fix this I did this:

```
export http_proxy=1
export ftp_proxy=1
```

(This works cuz I've looked at the source, where they have a bunch of if statements that look to ensure this is set.)

After more issues, I discovered I needed more dependencies in order to get this installed. Here's the list overall:

* libssl-dev
* perl-doc
* libxml-libxml-perl
* uuid-dev

I, however, did have to install a package using cpan cuz the installer failed, however if you run through the above dependencies first, you should be okay. Regardless of struggling with this, I still had issues:

```
The following Perl modules were found on the system but may be too old to work
with vSphere CLI:

Compress::Zlib 2.037 or newer
Compress::Raw::Zlib 2.037 or newer
IO::Compress::Base 2.037 or newer
IO::Compress::Zlib::Constants 2.037 or newer
```

But vmware-cmd appears to work ok.

This was accomplished using the latest version of vmware-cli and ubuntu at the time of this writing.

UPDATE:

On my Centos 6.3 system, I ran into the following issue:

```
cpan[1]> install UUID
<snip>
CPAN.pm: Building L/LZ/LZAP/UUID-0.05.tar.gz

Checking if your kit is complete...
Looks good
Writing Makefile for UUID
Writing MYMETA.yml and MYMETA.json
cp UUID.pm blib/lib/UUID.pm
/usr/bin/perl /usr/share/perl5/ExtUtils/xsubpp -typemap /usr/share/perl5/ExtUtils/typemap UUID.xs > UUID.xsc && mv UUID.xsc UUID.c
gcc -c -D_REENTRANT -D_GNU_SOURCE -fno-strict-aliasing -pipe -fstack-protector -I/usr/local/include -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexcepti
ons -fstack-protector --param=ssp-buffer-size=4 -m64 -mtune=generic -DVERSION=\"0.05\" -DXS_VERSION=\"0.05\" -fPIC "-I/usr/lib64/perl5/CORE" UUID.c
UUID.xs:5:23: error: uuid/uuid.h: No such file or directory
<snip>
UUID.xs:30: warning: unused variable ‘str’
make: *** [UUID.o] Error 1
LZAP/UUID-0.05.tar.gz
/usr/bin/make -- NOT OK
Running make test

Can't test without successful make

Running make install
Make had returned bad status, install seems impossible
Failed during this command:
LZAP/UUID-0.05.tar.gz : make NO
```

In order to fix this crap, you'll need to install yet another library:

```
libuuid-devel
```

Resources:

* [https://pay.reddit.com/r/perl/comments/qw34z/problems_installing_uuid](https://pay.reddit.com/r/perl/comments/qw34z/problems_installing_uuid)
* [http://blog.erben.sk/2013/01/28/installing-vsphere-cli-on-centos-6](http://blog.erben.sk/2013/01/28/installing-vsphere-cli-on-centos-6)

