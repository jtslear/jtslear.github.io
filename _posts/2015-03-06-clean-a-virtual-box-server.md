---
layout: post
title: Clean a virtual box server
date: 2015-03-06 09:38:53.000000000 -07:00
type: post
published: true
status: publish
categories: []
tags: []
---
So at the company I work for these days we utilize a nifty vagrant setup that utilizes virtualbox for test kitchen.  Sometimes when jobs fail we get lingering vboxes just kinda sit around eating space until we're alerted about it.  Here's a command that we use to roll through our various build users and removes things that probably shouldn't exist

```
find /home/*/VirtualBox\ VMs/ -mtime +5 | sed 's/VirtualBox\sVMs/VirtualBox\ VMs/g' | xargs rm -rf
for vm in `VBoxManage list vms| awk '{print $2}' |sed 's/[{}]//g'`; do VBoxManage unregistervm $vm; done
```
