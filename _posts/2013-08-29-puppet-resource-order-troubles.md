---
layout: post
title: Puppet Resource Order Troubles
date: 2013-08-29 20:58:57.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- linux
- puppet
---
### The Problem:

After executing the puppet agent, things break when a package is trying to install, because the apt sources list is out of date.

### My Example:

Here's an example manifest with some attempts at creating a specified order. [Check it out](https://gist.github.com/jtslear/6373146):

```
class play {
 class { "apt":
    always_apt_update =&gt; true,
    before =&gt; Class["mysql"],
  }
  class { "mysql":
    require =&gt; Class["apt"],
  }
  Class["apt"] -&gt; Class["mysql"]
}
```

When the puppet agent is first run the mysql class does not complete because apt-get needs to update.  When viewing the logs it is apparent that puppet continues to try to run the install for mysql first despite my attempts at pushing it down the list.

### The Reason for Failure:

Puppet is unable to maintain resource ordering with complex modules.  Both the apt and the mysql modules have nested classes where puppet does not assign the resource order to the inner class.  When this happens, puppet behaves the way it wants.

### The Solution:

Staging. [Here's an example](https://gist.github.com/jtslear/6385030)

```
class play {
  stage { 'first':
    always_apt_update =&gt; true,
    before =&gt; Stage["main"],
  }
  class { "apt":
    stage =&gt; first
 }
  class { "mysql": }
}
```

There's only one stage created by default, "main."  We are now telling puppet to "pre stage," if you will, the apt class before the "main" stage runs.

Now if one steps back and thinks about it (without all this resource ordering jibberish), if puppet is run a second time, of course it will work, cuz during the first run, eventually puppet does run the apt module to complete it's task, putting the system in a better state.  But this is not preferred.  Subsequently, if the apt sources list goes out of date later, it will take yet another run before puppet is successful.

### Must Reads:

* [docs.puppetlabs.com...run_stages](http://docs.puppetlabs.com/puppet/3/reference/lang_run_stages.html)
* [stackoverflow.com...how-to-use-puppet-parameterized-classes-to-force-order-of-resources-applied](http://stackoverflow.com/questions/11675102/how-to-use-puppet-parameterized-classes-to-force-order-of-resources-applied)
* [forge.puppetlabs.com...stdlib](http://forge.puppetlabs.com/puppetlabs/stdlib)
