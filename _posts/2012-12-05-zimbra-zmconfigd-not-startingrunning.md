---
layout: post
title: Zimbra zmconfigd not starting/running
date: 2012-12-05 01:01:04.000000000 -07:00
type: post
published: true
status: publish
categories: []
tags:
- linux
- netcat
- watch
- zimbra
- zmconfigd
---
### The Problem:

zmconfigd status would indicate that it is not running when viewing the services status on the admin console as well as output from `zmcontrol status`

### The fix:

Ensure that the `which` command is installed. After that is completed remove a pid file. Then start the service.

```
# yum install which
# sudo su - zimbra
$ rm -rf /opt/zimbra/log/zmconfigd.pid
$ zmconfigdctl start
$ zmcontrol status
```

### Why?
My suspicion can be found in the source code for zmconfigdctl:

```
NC=`which nc 2>/dev/null`; NC=${NC:-`which netcat 2>/dev/null`}
```

Obviously if both `which` and `nc` are missing, zimbra will fail. Why?

```
status=`echo STATUS | $NC -w 10 -i 1 localhost ${zmmtaconfig_listen_port} 2>/dev/null`
```

This is used to see if it's running. Zimbra will notify you during install that netcat is required but does not complain about the `which` command. Even my manager complains that I build servers light on installed packages. Add this to the list of "problems-john-created-due-to-missing-dependencies."

This service's purpose is to monitor for specific configuration changes and restart necessary services in order for those new configurations to take place automatically

Now I know.

### Useful:
* [Zimbra Wiki](http://wiki.zimbra.com/wiki/Zmconfigd)
