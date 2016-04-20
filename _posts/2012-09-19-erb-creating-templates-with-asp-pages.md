---
layout: post
title: ERB Creating Templates with ASP pages
date: 2012-09-19 11:23:54.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- chef
- erb
---

I've been working on utilizing Chef from Opscode lately. One of the awesome features is the ability to create [templates](https://docs.chef.io/resource_template.html). A template of a file utilizes ERB for any customization. But what if you are creating template of an ASP page? ERB and ASP use very similar code tags. The following example will cause an issue and chef will fail:

```
<%hdr="<%= @site_name %>"%>
```

My recipe is only passing in a variable of site_name. Chef has no clue how to inperpret anything else and the erb template creation fails. There's a way around this:

```
<%%hdr="<%= @site_name %>"%>
```

Add the extra "%" so "&lt;%%" will ignore the ASP code tag. And it will do exactly that, escape the &lt;%, but what one needs to know, is that this will not produce expected results. In this case the entire line is processed and ignored until the closing tag. So the result _after_ template creation is:

```
<%hdr="<%= @site_name %>"%>
```

To correct this one would hope that simply adding the closing tag the statement as such:

```
<%%hdr="%><%= @site_name %>"%>
```

After which, the template will be processed,,, kinda....:

```
<%hdr="%>The Botched Network" %>
```

NOT what I want it to do. After mucking around with this for a long time, I'm at a loss. I ended up storing the entire line that I needed into a variable. I relinquish my ERB learning for now...

Sources:

* [ruby-doc...erb](http://ruby-doc.org/stdlib-1.9.3/libdoc/erb/rdoc/ERB.html)
* [stackoverflow...how-do-i-escape-erb-code-in-fixtures](http://stackoverflow.com/questions/2322413/how-do-i-escape-erb-code-in-fixtures)

![y u no worky]({{ site.baseurl }}/assets/screen-shot-2012-10-05-at-21-24-56-pm.png)

