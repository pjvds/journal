---
layout: post
title: "Jasic: A Complete Interpreter in One Java File"
categories: code jasic language parsing
---
I just put the finishing touches on a tiny little interpreter project: Jasic.
Jasic is a dialect of the [original BASIC](http://en.wikipedia.org/wiki/Dartmouth_BASIC) programming language. It lacks
functions and scope, but it's a usable language. Even on an old Apple IIe, it
was powerful enough to get me hooked on coding for life. Also, you can [draw a
Mandelbrot set](http://github.com/munificent/jasic/blob/master/sample/mandel.jas) with it.

I wanted to see if I could fit a complete interpreter in a single readable
Java file. It worked out better than expected, so I went ahead and cleaned up
the code and commented the hell out of it. If you've always wanted to learn
more about interpreters, now you've got a little primer.

Here's a little code to give you a flavor of the language:

{% highlight finch %}
' initialize the loop counter
count = 10

' stop looping if we're done
top:
if count = 0 then end
print "Hello, world!"

' decrement and restart the loop
count = count - 1
goto top
end:
{% endhighlight %}

Old school! All of the code is [right here in Jasic.java](http://github.com/munificent/jasic/blob/master/com/stuffwithstuff/Jasic.java). There's also a
[README](http://github.com/munificent/jasic/blob/master/README) to help get you started. What are you waiting for?

<blockquote class="update">
<p><em>Update 2011/09/9:</em> Moved JASIC from bitbucket to github.</p>
</blockquote>
