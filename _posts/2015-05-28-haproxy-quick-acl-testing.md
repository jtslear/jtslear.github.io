---
layout: dark-post
title: Haproxy Quick ACL Testing
date: 2015-05-28 21:47:30.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- haproxy
---
This is just a very minor setup that I'm using that I thought I'd share for
testing acl rules and such.

Ruby sinatra is a great and very quick way to spin up a tiny webservice. By
default it'll run webrick which is fine for this method of testing.  Here's what
my app looks like:

```
require &#039;sinatra&#039;
set :port, ARGV[1]
get &#039;*&#039; do
  &quot;#{ARGV[0]}&quot;
end
```

And then I have this spun up via a very tiny barely functional init script (we<br />
use chef, I didn't feel like spinning up a ruby environment, so I hacked into<br />
chef's embedded ruby):

```
#!/bin/bash
/opt/chef/embedded/bin/ruby /root/test-web-app.rb success 4567 &gt;&gt; /dev/null 2&gt;&amp;1 &amp;
/opt/chef/embedded/bin/ruby /root/test-web-app.rb fail 4568 &gt;&gt; /dev/null 2&gt;&amp;1 &amp;
RETVAL=$?
exit $RETVAL
```

And then when I need to test a rule in haproxy I set it up like so:

```
backend success_backend
  server localhost 127.0.0.1:4567 check
backend fail_backend
  server localhost 127.0.0.1:4568 check
```

Then in my frontend I can create acl's and send them to either backend for quick testing.

```
acl successful url_beg /yay
use_backend success_backend if successful
default_backend fail_backend if failure
```

And then doing a test using curl we see that this worked well enough.

```
[root~]# curl http://localhost/yay
success
[root~]# curl http://localhost/aww
fail
[root~]#
```

This becomes limiting when you need to start messing with things like redirects
or if you need to pay attention to headers and cookies.  But for starting off,
especially learning and getting the hang of haproxy, this was very ideal.
Sinatra makes this incredibly easy for helping me test rewrites.

### Resources:
* [sinatrarb](http://www.sinatrarb.com)
* [haproxy](http://www.haproxy.org)
* [my deploy recipe](https://gist.github.com/jtslear/c731c1a591c73d9ad339)
