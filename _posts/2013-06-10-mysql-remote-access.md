---
layout: dark-post
title: mysql Remote Access
date: 2013-06-10 15:28:25.000000000 -06:00
type: post
published: true
status: publish
categories: []
tags:
- mysql
---
So, I don't do this often enough, but when I do, I forget how to do it EVERYTIME.  The below allows a specific user access to a specific database from a remote connection.

```
UPDATE db SET host='FQDN' WHERE db='database_name';
UPDATE user SET host='FQDN' WHERE user='user_name';
FLUSH PRIVILEGES;
GRANT ALL PRIVILEGES ON database_name.* TO 'user_name'@'FQDN' IDENTIFIED BY 'the_password';
```

FQDN refers to the server that is making the connection to the database server.
