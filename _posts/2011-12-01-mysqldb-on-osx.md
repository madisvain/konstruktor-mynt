---
layout: post.html
title: 'MySQLdb on OS X'
tags: ['os x', 'mysql']
---

Installing MySQL support for python on OS X has almost always been troublesome. Still seems to be so in Lion so I'll just add some quick notes how to do it cleanly and painlessly.

These instructions are intended to be used with the MySQL official Community Server release.

###PIP or easy_install
For installing python modules I use PIP but this also works with source or easy_install. At first installing with PIP would fail with the following tracepack.

~~~ { python }
File "setup_posix.py", line 24, in mysql_config
raise EnvironmentError("%s not found" % (mysql_config.path,))
EnvironmentError: mysql_config not found
~~~

So we need to set the correct mysql_config path in the for the current shell.

~~~ { shell }
PATH=/usr/local/mysql/bin:$PATH
~~~

This should fix the mysql_config not found problem.

~~~ { python }
sudo pip install mysql-python
~~~


###Source install
If you are doing a source install you'd edit the source of mysql-python's setup_posix.py line 26

~~~ { python }
mysql_config.path = "/usr/local/mysql/bin/mysql_config"
~~~
	
Fallows a regular source install

~~~ { python }
sudo python setup.py build
sudo python setup.py install
~~~

####libmysqlclient import error
If you'd try to use mysql-python right now you'd probably get another import error.

~~~ { python }
ImportError: dlopen(/Users/konstruktor/.python-eggs/MySQL_python-1.2.3-py2.7-macosx-10.7-intel.egg-tmp/_mysql.so, 2): Library not loaded: libmysqlclient.18.dylib
~~~

To fix this one let's just add a few symlinks.

~~~ { shell }
sudo ln -s /usr/local/mysql/lib/libmysqlclient.18.dylib /usr/lib/libmysqlclient.18.dylib
sudo ln -s /usr/local/mysql/lib /usr/local/mysql/lib/mysql
~~~

###Try it out
Well we have done all the fixes now and things should work. To be sure let's try importing it in a python interpreter.

~~~ { python }
import MySQLdb
~~~

If you get no errors then you have a working mysql-python module and can start working with MySQL.