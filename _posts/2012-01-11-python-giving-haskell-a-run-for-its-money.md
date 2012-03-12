---
layout: post.html
title: 'Python giving Haskell a run for its money'
tags: ['python', 'haskell']
---

Everybody knows [Haskell](http://www.haskell.org/haskellwiki/Haskell 'Python') beats [Python](http://python.org/ 'Python') in speed - always. The main reason being is that Haskell is compiled but Python interpreted. There have been some general posts about this all around. But lets give it a fair trial before finding guilty - lets add PyPy to the mix.

_[PyPy](http://pypy.org/ 'PyPy') is a implementation of Python written in Python as the name actually suggests. There are tests to suggest that it actually [outperforms CPython by quite a margin](http://speed.pypy.org/). And it does this by using [JIT (Just In Time Compiler)](http://en.wikipedia.org/wiki/Just-in-time_compilation, 'JIT Wikipedia') and better memory management. So it's a blazing fast solution but they have not stopped there. They have plans for supporting Python 3 in PyPy and numPy in PyPY._

[Previous posts](http://donsbot.wordpress.com/2007/11/29/holy-shmoly-ghc-haskell-68-smokes-python-and-ruby-away-and-you-can-parallelise-it-for-free/) have suggested that Haskell is 60 x faster then Python and [others](http://filoxus.blogspot.com/2007/12/holy-shmoly-haskell-doesnt-smoke-python.html) that maybe around 5x faster. This seemed a large cap - but these tests were done 2007. So I'm very eager to challenge them according to todays standards.

### Naive Fibonacci Algorithms
So the little scripts will be calculating and outputting fibonacci numbers which are are the numbers in the following integer sequence: 0, 1, 1, 2, 3, 5, 8, 13, ...

_By definition, the first two numbers in the Fibonacci sequence are 0 and 1, and each subsequent number is the sum of the previous two._

#### Haskell implementation

~~~ { haskell }
fib :: Int -> Int
fib 0 = 0
fib 1 = 1
fib n = fib (n-1) + fib (n-2)

main = forM_ [0..35] $ \i ->
    printf "n=%d => %d\n" i (fib i)
~~~


#### Python implementation
~~~ { python }
def fib(n):
   if n <= 1:
      return n
   else:
      return fib(n-1) + fib(n-2)
      
if __name__ == "__main__":
    for i in xrange(36):
        print "n=%d => %d" % (i, fib(i))
~~~

### Results
<table>
	<thead>
		<tr>
			<th>Haskell (optimized)</th>
			<th>PyPy</th>
			<th>Haskell (unoptimized)</th>
			<th>CPython</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>0.348 s</td>
			<td>2.054 s</td>
			<td>2.334 s</td>
			<td>25.996 s</td>
		</tr>
		<tr>
			<td>1 x</td>
			<td>5.9 x</td>
			<td>6.7 x</td>
			<td>~75 x</td>
		</tr>
	</tbody>
</table> 

These tests really show that PyPy is a huge improvement and it seems to be getting better by the day. After running these tests I was told that PyPy's main speedup is not in full force when calculating Fibonacci numbers as the outer loop probably never reaches JIT.

If you use Python and have not tried PyPy before - you really should. It makes a huge difference. PyPy is probably never going to be faster then compiled languages but it gets you closer!

_Edit: There are [several documented cases](http://morepypy.blogspot.com/2011/08/pypy-is-faster-than-c-again-string.html) where PyPy is faster then compiled languages like C_

#### Notes about testing
* GHCi, version 7.0.4 compiler for Haskell
* Python 2.7.1 interpreter
* PyPy 1.7.0
* Fibonacci was calculated for 36 recurrences
* For timing I used [unix time command](http://unixhelp.ed.ac.uk/CGI/man-cgi?time) before every command issued.

### PyPy for Python
PyPy is a project that Python really needs. It helps Python loose it's reputation of being just "fast enough" and becoming a litter more "fast". And I think Python needs this now more then ever as there are signs of people not being fully happy with [Python 3 current state](http://lucumr.pocoo.org/2011/12/7/thoughts-on-python3/), implementations and the process how we are transitioning there. There just aren't any real advantage over Python 2.

If Python 3 had PyPy support I think it would be easier to explain to ourselves why we need to port our code into Python 3. Armin Ronacher has already asked "Can we accept PyPy as a valid Python implementation that is worth considering as having an effect on how we write code?". And I think we should.

I hope we do.

_If you want to help towards Python 3 in PyPy please consider donating. [Call for donations](http://pypy.org/py3donate.html)._
