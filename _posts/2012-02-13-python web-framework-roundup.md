---
layout: post.html
title: 'Python web framework roundup'
tags: ['python', 'framework']
---

A lot of web frameworks have been built for Python. There are even tutorials around the web for rolling your own web framework in Python. That is because it's really that easy. In the end this has led to a lot of great frameworks and a lot of bad ones also. So lets round them up and separate the wheat from the chaff.

### High rollers
These are the main Python web frameworks people usually talk about.

<table>
	<thead>
		<tr>
			<th>Name</th>
			<th>Version</th>
			<th>Last update</th>
			<th>First release</th>
			<th>LOC</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Django</td>
			<td>1.3.1</td>
			<td>2011-09-09</td>
			<td>2005</td>
			<td>115759</td>
		</tr>
		<tr>
			<td>Flask</td>
			<td>0.8</td>
			<td>2011-09-29</td>
			<td>2010</td>
			<td>4681</td>
		</tr>
		<tr>
			<td>Bottle</td>
			<td>0.10.9</td>
			<td>2012-02-11</td>
			<td>2009</td>
			<td>4634</td>
		</tr>
		<tr>
			<td>Tornado</td>
			<td>2.2</td>
			<td>2012-01-30</td>
			<td>2009</td>
			<td>11701</td>
		</tr>
		<tr>
			<td>Cherry.py</td>
			<td>3.2.2</td>
			<td>2011-10-19</td>
			<td>2002</td>
			<td>18828</td>
		</tr>
		<tr>
			<td>web.py</td>
			<td>0.36</td>
			<td>2011-07-04</td>
			<td>2006</td>
			<td>7398</td>
		</tr>
		<tr>
			<td>Brubeck*</td>
			<td>0.3.7</td>
			<td>2011-12-20</td>
			<td>2011</td>
			<td>1525</td>
		</tr>
	</tbody>
</table>

<p style="font-size: 10px;">
	* With the exception of Brubeck but I think we are going to be hearing a lot about this new framework because it's not yet another Python web framework.<br/>
	** There or many others Zope, Pylons, Pyramid - I have left them out as I have no personal experience with them.<br/>
	*** Flask with Werkzeug, Jinja2 code adds up to ~35k lines of code<br/>
	**** Lines of code - means actual Python code. Counted with <a href="http://cloc.sourceforge.net/">CLOC<br/><br/>
</p>
  
### [Django](https://www.djangoproject.com/)
<a href="https://www.djangoproject.com/"><img src="/static/blog/images/content/django.png" alt="Django" style="float: right;" /></a>
This one probably is the best known and most used Python web framework around. And I have no doubt that it stands up to its name. It has everything - batteries included. Django also has the biggest community with the most packages around for almost anything you can think of. It is exceptionally well documented but there are some corner cases which you'll have to search for on StackOverflow or look for in the source.

Django uses convention over configuration which is a treat for beginners to get going and still flexible enough for complex applications. Django also deliveres on rapid development and clean, pragmatic design as they promise.

Django [can be run minimalistically](http://olifante.blogs.com/covil/2010/04/minimal-django.html) with the following to produce a Hello world!

~~~ { python }
import os
from django.conf.urls.defaults import patterns
from django.http import HttpResponse
filepath, extension = os.path.splitext(__file__)
ROOT_URLCONF = os.path.basename(filepath)

def hello(request):
    return HttpResponse('Hello World!')

urlpatterns = patterns('', (r'^/$', hello))
~~~

The only thing that I really don't like is that its batteries are welded shut. You can't really change parts of it because you want to - they say you can but in reality you will break things if you do. To explain this - for example if I wanted to use SQLAlchemy as the ORM then SQLAlchemy would brake the Admin, Auth, Form ... well all most everything. So if you use Django make the commitment to using it and the tools provided with it.

**Fast overview:**  
[Django at a glance](https://docs.djangoproject.com/en/1.3/intro/overview/)

**Built on Django**  
[Disqus](http://disqus.com/ 'Disqus'), [EveryBlock](http://www.everyblock.com/ 'Every Block'), [Guardian (newspaper)](http://www.guardian.co.uk/ 'The Gurdian'), [Firefox add-ons (Mozilla)](https://addons.mozilla.org/en-US/firefox/ 'Firefox Add-ons')

<br/>
### [Flask](http://flask.pocoo.org/)
<a href="http://flask.pocoo.org/"><img src="/static/blog/images/content/flask.png" alt="Flask" style="float: right;" /></a>
Armin Ronacher is the guy behind this ingenious web framework. It names itself a micro framework - it basically is _except it's a micro glue framework_. It glues together Werkzeug and Jinja into a easy to use solution (adds up to ~35k lines of code). So it's modular by design and can be easily extended.

Flask also has a lot of [extensions](http://flask.pocoo.org/extensions/) to use and fast growing user base. It's well documented and even has a guide for [common patterns](http://flask.pocoo.org/docs/patterns/#patterns) which comes very handy. Flask is very easy to use and a basic Hello world application is just 3 _(7)_ lines of code.

~~~ { python }
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run()
~~~


In my opinion it's biggest strength is also one of it's biggest (little) weaknesses - Flask does not enforce a specific ORM. This makes writing extensions a bit harder as you'll probably want to use some kind of database layer - but which one? Python has quite a few of these as you know. This very blog also runs Flask on Google App Engine. And it sure is handy because Google Datastore is unlike any other database out there. So there are times when not enforcing a specific ORM is a good thing.

It's an exceptionally good framework for building a new site but I would not recommend it for total beginners or people who don't really care too much about how things work but just expect them to.

**Fast overview:**  
[Flask quickstart](http://flask.pocoo.org/docs/quickstart/)

**Built on Flask**  
[Dev news aggregator for Battlefield3](http://bf3.immersedcode.org/), [Media Queries](http://mediaqueri.es/), [Learn buffet](http://www.learnbuffett.com/), [Konstruktor (appengine)](www.konstruktor.ee, 'Konstruktor by Madis Väin')

<br/>
### [Bottle](http://bottlepy.org)
<a href="http://bottlepy.org"><img src="/static/blog/images/content/bottle.png" alt="Bottle" style="float: right;" /></a>
This framework is relatively new. It has been influenced by Sinatra - the micro framework for Ruby. Bottle is actually what you could call a micro framework - it consists of only around 4.5k lines of code. And I think it's the best *truly micro* framework there is for Python to date. Bottle has no other dependencies then the Python Standard Library and it even has it's own little templating language. For what it's little codebase achieves that’s very good. Bottle also has python 3k support which Flask and Django don't yet have.

Documentation for bottle is extensive and holds solutions to things it yet does not do. The Hello world example actually resembles Flask very closely. It's also a 3 liner and uses decorators for defining routes.

~~~ { python }
from bottle import route, run

@route('/hello/:name')
def hello(name):
    return '<h1>Hello %s!</h1>' % name.title()

run(host='localhost', port=8080)
~~~

I know bottle itself takes bride in it being a one file solution but it's actually quite hard to find things in a file ~3k lines long. There is code scattered around everywhere and looks like a mess (probably makes sense to someone).

Bottle would be a good choice for a very small project or experimenting with Python and WSGI. It would probably not be a good idea to start a bigger project on it as it does not have many plugins written for it and you'll probably soon run into a mess somewhere. You could implement 3rd party libraries yourself though.

**Fast overview:**  
[Bottle tutorial](http://bottlepy.org/docs/dev/tutorial.html)

**Built on Bottle**  
[Plush (monitoring)](http://pulsh.com/), [Hobo (Blog enginee)](http://andrewnelder.github.com/hobo/)

<br/>
### [web.py](http://webpy.org/)
<a href="http://webpy.org/"><img src="/static/blog/images/content/webpy.jpeg" alt="web.py" style="float: right;" /></a>
Some long time ago this was used for reddit. When it was still young growing popular. And it handled the traffic well because it has minimal overhead. Everything a framework needs is implemented but in a quite low level. They call themselves the anti framework framework and by that they mean web.py will not get in your way if you want to write a web application in Python.

It has class based views and is actually quite easy to use. Standard [app skeleton](http://webpy.org/skeleton/0.3) is very simple and straight forward. web.py is also packages nicely into sorted files and folders.

~~~ { python }
import web
        
urls = ('/(.*)', 'hello')
app = web.application(urls, globals())

class hello:        
    def GET(self):
        return 'Hello, World!'

if __name__ == "__main__":
    app.run()
~~~

Sadly this library has fallen victim to the latest craze of rails style frameworks that do a lot of work and magic for you. Also it has it's own modules for everything - templatin, forms, database. They probably are not so well maintained as other similar libraries. But there still is active development and somebody is using it so it has not and probably will not die out.

**Fast overview:**  
[web.py tutorial](http://webpy.org/docs/0.3/tutorial)

**Built on web.py**  
[Yandex (russian search engine)](http://www.yandex.ru/), [Telephone directory (Switzerland)](http://www.local.ch/)

<br/>
### Tornado
<img src="/static/blog/images/content/tornado.png" alt="Tornado" style="float: right;" />
Tornado is not purely a web framework but also a web server. It was originally developed for FriendFeed, which was acquired by Facebook in 2009. It's intention is to solve the problem of real-time services as they have thousands of live connections. Tornado does this by non blocking and queuing the connections with epoll or kqueue.

Tornado documentation is hardcore technical. It's not intended for beginners but intermediate or advanced users will find it satisfying. The Hello World app goes as follows.

~~~ { python }
import tornado.ioloop
import tornado.web

class MainHandler(tornado.web.RequestHandler):
    def get(self):
        self.write("Hello, world")

application = tornado.web.Application([
    (r"/", MainHandler),
])

if __name__ == "__main__":
    application.listen(8888)
    tornado.ioloop.IOLoop.instance().start()
~~~

I have never used this for a actual project as I have not found the need for this yet. It also seems that by default Tornado passes the WSGI layer because according to their words WSGI does not do asynchronous. Tornado sure is perform ant but as a database call would actually still block the IO Loop - I'm not yet convinced.

**Fast overview:**  
[Tornado overview](http://www.tornadoweb.org/documentation/overview.html)

**Built on Tornado**  
[Too cool for me](http://toocoolfor.me/), [FriendFeed](http://friendfeed.com/)

<br/>
### CherryPy
<img src="/static/blog/images/content/cherrypy.png" alt="Tornado" style="float: right;" />
This is one of the oldest web frameworks for Python. CherryPy as a framework is not very widely used and it's probably better known as a web server then a web framework. For serving requests CherryPy also uses queuing for performance but does it through [thread-pooling](http://en.wikipedia.org/wiki/Thread_pool_pattern). 

Documentation for is quite sparse actually but it covers the main topics. CherryPy also has python 3 support. Syntax for a Hello World is quite beautiful I must say.

~~~ { python }
import cherrypy
class HelloWorld(object):
    def index(self):
        return "Hello World!"
    index.exposed = True

cherrypy.quickstart(HelloWorld())
~~~

**Fast overview:**  
[CherryPy introduction](http://docs.cherrypy.org/stable/intro/index.html)

**Built on CherryPy**  
[YouGov](http://global.yougov.com/), [Cuil search engine (ended 2010)](http://en.wikipedia.org/wiki/Cuil)

<br/>
### Brubeck
<img src="/static/blog/images/content/brubeck.png" alt="Brubeck" style="float: right;" />
This is a completely new approach to a python web framework. It does not use WSGI but uses a language agnostic web server called Mongrel2 - this leaves only the request handling to Python. Requests are handled as coroutines (greenlets).

For models they use DictShield library, which means that plugins for different database layers could be written on top of it.

Documentation for Brubeck is minimal but once you look at the source you realize that there really is not much to it. So it’s a very young and still a developing framework. Hello world sample looks very nice also.

~~~ { python }
class DemoHandler(WebMessageHandler):
    def get(self):
        self.set_body('Hello world')
        return self.render()

urls = [(r'^/', DemoHandler)]
mongrel2_pair = ('ipc://127.0.0.1:9999', 'ipc://127.0.0.1:9998')

app = Brubeck(mongrel2_pair=mongrel2_pair,
              handler_tuples=urls)
app.run()
~~~

The only thing is that when using Brubeck you will have to stick with the Mongrel2 web server also.

**Fast overview:**  
[Brubecks design](http://brubeck.io/design.html)

**Built on Brubeck**  
[ListSurf](https://github.com/j2labs/listsurf)
