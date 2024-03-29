---
layout: post.html
title: 'Django - the undocumented settings'
tags: ['Django', 'python']
---
There are some things that can make your work with Django more effective. Many of which are not in the documentation and you just figure them out once you are confronted with a problem. This series "Django - the undocumented" posts will highlight some of the tricks I have learned and used over the years.

## settings.py
I'm starting with settings as this is the first thing you will set up with a new Django project and the first thing you have to realize is that settings.py is just Python. It can handle all sorts of python code that you throw at it.

### Absolute paths
This on is a MUST DO for everyone. Writing the absolute paths and keeping them up to date inbetween development and deployment would be a headache.

So use python to ease the generation of absolute paths for you.

~~~ { python }
import os
PROJECT_DIR = os.path.dirname(__file__)

MEDIA_ROOT = os.path.join(PROJECT_DIR, 'media')
~~~

### Staticfiles and templates
When Django 1.3 introduced the staticfiles app I was happy but confused. Where do I put the base templates and static files that don't belong to any app but still maintain the project structure.

So for this I got inspiration from Rails. Just keep them in a "assets" folder and include them in STATICFILES_DIRS and TEMPLATE_DIRS.

~~~ { python }
STATICFILES_DIRS = (
	os.path.join(PROJECT_DIR, 'assets', 'static'),
)
TEMPLATE_DIRS = (
	os.path.join(PROJECT_DIR, 'assets', 'templates'),
)
~~~

### Local settings
So there are some different opinions on this but I like to keep development settings in the settings.py file and overwrite them with local settings. This makes it possible to have a settings_local.py file in the deployment server with it's own SECRET_KEY and DEBUG set to False.

_Set this at the very end of settings.py . And remember to keep settings_local.py out of version control._

~~~ { python }
try:
	from settings_local import *
except ImportError:
	pass
~~~

If your interested in even more modularity - then you could read the ["Settings in Django"](http://justcramer.com/2011/01/13/settings-in-django/) by David Cramer

### Database configuration
When setting up a new project and doing initial testing I find working with SQLite easiest. It has zero configuration and if you stick to ORM (no custom SQL) then you can make the switch afterwards with no problems.

~~~ { python }
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'database',
    }
}
~~~

##### Checklist
* Just remember to ad database to .gitingore
* If something goes wrong and you want to start over - then just rm -f database and run a syncdb.
* Remember to test with a reale database before actual deployment because some errors pass with sqlite.

### Makefile for command chains
Django has it's own management commands system but it's quite clunky to write commands for. So I just use a Makefile for the simple things.

This allows me to be a bit lazy and automate some of the things I do regularly. Just type "make run" to run the development server or "make reset" for reseting the whole database, load the fixtures and create a new superuser without having to type everything again and again.

_Makefile_

~~~ { makefile }
run:
	python manage.py runserver

reset:
	rm -Rf database
	rm -f database.sqlite
	yes no | python manage.py syncdb
	python manage.py loaddata currencies
	python manage.py createsuperuser --username=madis --email=user@host.com
~~~

### Logging DB queries
While I love the looks of Django-debug-toolbar for debugging I hate how slow it actually loads. So i like to keep things lightweight and log the DB queries to the shell. Just add the following.

_settings.py_

~~~ { python }
import logging
~~~

~~~ { python }
LOGGING = {
    ...
    'handlers': {
        ...
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
        }
    },
    ...
}
~~~

~~~ { python }
LOGGING = {
    ...
    'loggers': {
        ...
        'django.db.backends': {
			'level': 'DEBUG',
            'handlers': ['console'],
        }
    },
    ...
}
~~~

So this just outputs every query to the shell. If thats too verbose for you then you can set up an [aggregation middleware for queries by Jamie Matthews](http://dabapps.com/blog/logging-sql-queries-django-13/)

### .gitignore
The gitignore template is pretty standard.

~~~ { bash }
.DS_STORE
.log
*.pot
*.pyc
database
settings_local.py
media/*
static/*
~~~

### Debugging on demand
This is a bit outside of settings but if you want to raise the debugging page on demand then just write the following into your view.

~~~ { python }
assert False
~~~

_In the next post I'll write about undocumented authentication and user registration._