---
layout: dark-post
title: Cygwin Useful?
date: 2011-02-20 19:59:44.000000000 -07:00
type: post
published: true
status: publish
categories: []
tags: []
---
Yes.

I use cygwin.

My daily job entails me to manage both windows and linux machines on a regular basis. The environment that I work in is mostly windows machines, however there are a small number of our systems that do run linux. For basic administration, you'd think PUTTY is the way to go, simply log in and run with it. But from a standpoint of security, and to not interfere with the performance of a production system, you really ought not log into any production server. As an avid linux user, I have a decent sense of awareness as to know when doing X might effect Y. But I'm not the greatest of typists. You never know.

So I've decided to do basic linux tasks from my own personal machine. Having cygwin may be one of those insanely convoluted ways of running linux commands and tasks while on a windows machine, but there's one massive benefit from which I like it so much. I can mess with files on a windows machine with ease. Plus, I can use PUTTY to log into the SSH server on my machine. You don't realize how much hatred comes with the use of the windows command line. Granted I know nothing about Power Shell. Go figure...

So then at this rate, why not create a virtual machine and run it inside of windows?  Performance. Why not use a linux as the main system and run a windows virtual machine?  Performance. I know that may sound silly, but as a power user, there are way to many tasks or applications that I need to manage quickly. And having the admin kit come up very quickly for any service helps me out. I'm not a fan at all of running VM's on my business machine simply because I am a power user and I will starve the resources from my machine way too quickly. Now if they would simply upgrade my RAM,,, perhaps.

I also don't know of a solution that offers the ability to run a linux box with a virtual machine platform with the ability to manage the files on the physical box. VMware Workstation might, but it's hefty, requires a license, and running it's services are rather taxing on my puny little workstation. As far as I know qemu and VMware player do not contain the capability to share files within each other. The only solution that I do know of, is Parallels, but I don't run an Apple at the office.

Cygwin I utilize lightly, and has the minimalist features that I need to be able to what I need to do for my environment. I can login from a server to my machine to pick up files or check something out. I can scp something from my machine to a server. And as another example, I could run wget on a website to a production or another linux server...  But I run accross many websites that the link to download something, is a javascript, or php redirect. Download it to my desktop, use cygwin to scp it on over. Convoluted?  Yes, I agree. Though this make my life a bit easier. Since I'm also a perl user, this allows me to test scripts a bit easier too. With cygwin you have full access to the windows environment. Any command that works in windows, cygwin will pull off with great effort. If I installed a ruby environment on my system, cygwin will be able to rock it out.

If I had any complaint, it'd be the slashes. I use `pstools` greatly, and this is actually one of those programs that simply doesn't work out so hot due to the nature of the back slashes. By no means is anything perfect.

I do have my own test machine that runs CentOS, however, I do like having cygwin around at all times. I'm not trying to sell cygwin, I'm just letting you know that I like it. At least I'm not [this bad](http://www.youtube.com/watch?v=SXmv8quf_xM).

[Here is](http://www.noah.org/ssh/cygwin-sshd.html) how to set up cygwin's SSH to run as a service. Note that if you run firewalls, you may need an exception added, as well as should you choose to connect from a different machine.
