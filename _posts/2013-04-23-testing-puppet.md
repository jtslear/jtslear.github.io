---
layout: post
title: Testing Puppet
date: 2013-04-23 07:27:58.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- puppet
- ubuntu
---
### Requirements:

2 ubuntu servers

### Notes:

* I don't have DNS in my test environment, hence the host file modifications</li>
* The domain for my test network is '.home'</li>
* The IP of puppet master is 192.168.1.16 and hostname of puppetmaster</li>
* The IP of my puppet client is: 192.168.1.17 and hostname of puppetclient</li>

### Get Started:
#### On the puppet server:
Install puppet

```
apt-get install puppetmaster

```

Modify "DNS"

```
vim /etc/hosts

```

Modify the below (this is so the server knows who the client is):

```

127.0.0.1 localhost.localdomain localhost puppet
192.168.1.17 puppetclient.home puppetclient

```
Get my repo

```
cd /etc/puppet
git clone git@github.com:jtslear/bearded-octo-hipster.git

```

Restart puppet for good measure:

```
service puppetmaster restart

```

Ensure it starts appropriately, check log files, etc...

#### On the puppet client

Install puppet

```
apt-get install puppet

```

Modify "DNS"

```
vim /etc/hosts

```

Insert the below (this is so the client knows who the server is):

```
192.168.1.16 puppetmaster.home puppetmaster puppet

```

Configure the service to start

```
vim /etc/default/puppet

```

Modify the below appropriately:

```
START=yes

```

Then start the service:

```
service puppet start

```

### Complete Configuration
#### Server Certificate

We need to accept the cert provided by the client, on the server:

```
sudo puppetca --sign puppetclient.home

```

On the client, let's restart puppet, and wait for all to install:

```
service puppet restart
tail -f /var/log/syslog

```
