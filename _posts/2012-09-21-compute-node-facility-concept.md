---
layout: post
title: Compute Node Facility Concept
date: 2012-09-21 10:50:18.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- concept
- hpc
---
I've always had a dream of working in a datacenter in an environment with thousands of servers that work on some sort of High Performance Computing solution. I have zero experience, but here's my day dream:

### The Node:

The actual compute node consists of some sort of server that contains one or more GPU's, the most and fastest CPU cores, a good chunk of RAM, no RAID card, and no hard drives. My imagination leads me towards a solution similar to utilizing a [Dell C410x](http://www.dell.com/us/enterprise/p/poweredge-c410x/pd) or the [Cubix GPU Xpander](http://www.cubix.com) with some sort of Host Interface Card to interconnect the systems providing massive GPU compute power. An individual server would be tied to that system and provided a certain amount of GPUs. This type of setup allows us to provide smaller computers without the need for large motherboards with many PCI interfaces. In this case only a single PCI card is required and a set of one or more GPU's can be mapped to the node. This allows high CPU/GPU per U ratio. Check out [Dell C410x and C6100 Solution](http://en.community.dell.com/techcenter/high-performance-computing/w/wiki/hpl-performance-on-pe-c6100-and-c410x.aspx) for some details on a solution that Dell provides. Check out [Cubix Xpander Rackmount](http://www.cubix.com/xpander-rackmount-elite) for proposed solutions from Cubix.

The node would utilize PXE booting capabilities to download an OS that is provided via tftp of which would run entirely inside of RAM. The OS, dubbed 'image' for the rest of this document is discussed in more detail below.

### The Management Server:

The management server is the system that runs the tftp service that all nodes boot to and download the image. This server is setup in a fashion to have the best network connectivity to send the linux image down to each node quickly. To ensure that he can do this very well, I would suggest having a RAM disk with which the image for the nodes can be stored. A reverse proxy running a couple of back end tftp servers may be very beneficial. If the power goes out, thousands of servers asking for an image is bound to slow down this configuration. This is a risk, but at the cost of avoiding installing hard drives and managing that extra hardware on every node.

### The Distribution Server:

As a prefix, I have no experience in this field. The distribution server would be the head hancho that heads all requests for the compute services. Maintaining inventory for all workloads and distributing them to all the nodes in the facility. This server would be the proxy between the data that is submitted for processing and all of the nodes that perform the compute.

### DHCP Server:

Keeping with the times, let us run a full IPv6 Environment. It's been written about, lets rock with it. Special option for pxe booting to send nodes to talk to the management server for tftp services will be required.

### The Node OS (Image):

The nodes will be running a very lightweight version of Linux that has been stripped down and heavily modified to rid of as many packages as possible. The kernel would be seriously stripped down to whatever is required to run the system as well as any services that would need to run. Packages left in the image would only be those that are required for services to run. The node is not meant to be a device to do any troubleshooting. If there is a problem with it, reboot it. If there is a suspect hardware issue that may be causing problems, take out the node and utilize a different image of Linux that would be utilized to help a technician discover any potential issues. Running thousands of computers allows this to occur without any major impact to business

### Node Compute Cycle:

Over time, updates can be implemented in the image that is built for the nodes. New drivers, patches to packages, updates to scripts and services will warrant the need to download a new image or restart services. In order to remove human interaction and provide the best possibility that nodes will get updates, I envision a service that runs on the node that recognizes what the node is doing at any given time.

This service would also check the management server for an updated image. Should a new image be available it will need some way to notify the compute service or the distribution server to prevent this node from receiving another compute data set. This purpose would be to enable the node to complete its current compute data set, then reboot upon completing. A full cycle will appear as follows:

* Node hardware boots, PXE boot, download image, OS boots
* Services begin, node monitor agent checks in, compute service starts
* Node begins compute
* Management service checks into the management server
* Should an updated image be available, a flag is set that would instruct the node to reboot at the completion of the latest compute
* Node reboots

### Network:

Network connectivity would involve switches that support binding multiple Ethernet links together for nodes that may have more than one Ethernet port. Jumbo Packets shall be enabled on all network devices. I imagine redundancy built into the switch infrastructure. Two top of rack switches that are configured with stacking. Fiber connectivity between each switch, again with LACP enabled links to a set of core switches.

Nodes that have more than one NIC should be linked up utilizing LACP or something similar to allow the maximum bandwidth possible between the node and the network.

### Future Discussion:

* Power savings; turn off nodes when not in use
