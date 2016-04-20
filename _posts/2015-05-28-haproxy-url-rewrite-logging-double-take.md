---
layout: post
title: Haproxy URL Rewrite Logging Double Take
date: 2015-05-28 22:45:32.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- haproxy
---
Haproxy threw me for a loop today. It wasn't until I did a packet capture that
I discovered I was doing something correctly.

When doing URL rewrites, haproxy doesn't log the final outcome of a GET request
to its logs. So while during testing something wasn't working correctly, and
the client was sending the request properly, I was confused as I initially
expected the rewritten GET request to be logged when in fact it is not. Here's
my example haproxy configuration:

```
backend success_backend
reqirep ^([^\ :]*)\ (/[^/]+/[^/]+)(.*) \1\ /replaced\3
server localhost 127.0.0.1:4567
```

Here's my curl:

```
[root ~]# curl http://localhost/something1/something2/get
reached via replaced/get
[root ~]#
```

The response received is what I expected.

Here's what we see in the logs:

```
May 28 21:16:33 default-centos-66-x86-64 haproxy[22376]: 127.0.0.1:9856
[28/May/2015:21:16:33.831] tester14 success_backend/localhost 0/0/0/4/4 200 306
- - ---- 1/1/0/1/0 0/0 "GET /something1/something2/get HTTP/1.1"
```

Here's what show up during a packet capture:

```
21:16:33.831962 IP 127.0.0.1.9856 > 127.0.0.1.80: Flags [P.], seq
3445881728:3445881927, ack 2505859966, win 1024, options [nop,nop,TS val 3145623
ecr 3145623], length 199
E.....@.@.7.........&..P.c...\c~...........
./.../..GET /something1/something2/get HTTP/1.1
User-Agent: Mozilla/2.0 (compatible; MSIE 3.0; Windows 3.1)
Host: localhost
Accept: */*

21:16:33.832107 IP 127.0.0.1.42314 > 127.0.0.1.4567: Flags [P.], seq
3175948661:3175948869, ack 1519403319, win 1024, options [nop,nop,TS val 3145624
ecr 3145624], length 208
E.....@.@.]..........J...M!uZ.A7...........
./.../..GET /replaced/get HTTP/1.1
User-Agent: Mozilla/2.0 (compatible; MSIE 3.0; Windows 3.1)
Host: localhost
Accept: */*
client-ip: 127.0.0.1
```

That first packet is the curl going to haproxy, the second packet is haproxy
reaching out to the tiny tester app. Haproxy did exactly what was intended.

### Resources:
* [tiny tester app](https://gist.github.com/jtslear/055486fd91d82372dee8)
* [curlrc file](https://github.com/jtslear/dotfiles/blob/master/curlrc)
