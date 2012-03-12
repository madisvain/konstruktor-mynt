---
layout: post.html
title: 'SSH port forwarding for remote database administration'
tags: ['database', 'administration']
---

It's not a good idea to set up a phpMyAdmin on your production server mainly because of security reasons. Also if you are running a python only web application then there is no reason to load any PHP modules - they'd just eat memory.

So SSH allows you to forward ports from a remote machine to your local machine so you can access the database as if it was your local deployment without many of the security issues.

~~~ { shell }
ssh -N -L 3306:127.0.0.1:3306 user@remote-host.com
~~~
So this forwards the remote MySQL port to your local machines port 3306 to which you can connect with your favorite administrative tools. Be it phpMyAdmin, Sequel Pro or anything else.

_Options_

* -L [bind_address:]port:host:hostport
* -N Do not execute a remote command.