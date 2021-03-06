---
layout: post
title: "One and Only One"
categories: code language magpie
---
My little language [Magpie](http://bitbucket.org/munificent/magpie/) has a feature that may at first seem really
limiting: *all functions take exactly one argument and return one value, no
more, no less.* I'll try to explain why I made that choice, and some of the
surprising benefits of it.

It feels weird explaining a programming language that literally no one on
Earth is using, but my hope is that in the future people *will* be using
Magpie, and this might come in handy. At the very least, it'll help me
remember where I'm at.

First off, a quick primer in Magpie syntax. Here's a function to square a
number:

{% highlight magpie %}
Square (n Int -> Int) n * n
{% endhighlight %}

The name comes first, followed by the type signature in parentheses. To the
left of the `->` is the argument, to the right is the return type. The body of
the function is the single expression `n * n`. Like Ruby and most functional
languages, returning is implicit: a function returns the result of evaluating
its body.

You can call it like this:

{% highlight magpie %}
Square 3 // returns 9
{% endhighlight %}

Unlike C and other languages, function arguments aren't put inside
parentheses.

## So What If I Want to Pass in Multiple Args?

That's easy. Here's a function to multiply three numbers:

{% highlight magpie %}
Mult (a Int, b Int, c Int -> Int) a * b * c
{% endhighlight %}

You can call it like:

{% highlight magpie %}
Mult (2, 3, 4) // returns 24
{% endhighlight %}

## Hey! I Thought You Said Functions Always Take One Argument!

They do, they do. However, Magpie also supports [tuples](http://en.wikipedia.org/wiki/Tuple). A tuple lets you
make a single value by combining others together. In Magpie, tuples are
created using the comma operator:

{% highlight magpie %}
(1, 2)             // a tuple of two Int fields
(2, true, "three") // a tuple of three fields of different type
{% endhighlight %}

(They are also placed inside parentheses, but you can more or less ignore
that. It's more for precedence reasons than anything else. The comma is where
the magic is.)

So, when you see a call like `Mult (2, 3, 4)` what you're really seeing is
calling `Mult` with a single argument, the tuple `(2, 3, 4)`.

## Are You Just Playing Semantics?

No, this isn't just a semantic trick. `Mult` really does take a single value.
I'll prove it:

{% highlight magpie %}
// create a local variable and assign a tuple to it
def arg <- (2, 3, 4)

// pass the single argument to the function
Mult arg
{% endhighlight %}

This is perfectly legit in Magpie and doesn't require function overloading or
anything. In fact, this is kind of useful. When you start playing with
function references (i.e. callbacks), it's really convenient to be able to
pass around arguments to functions without needing to distinguish between how
many arguments it takes: it always takes one.

## Neat Trick. What About Functions With No Arguments?

That actually uses tuples too, sort of. You can make a tuple out of any number
of values, including one and zero. A single value _is_ a one-value tuple (a
monuple?). But you can also have a tuple with _no_ values, strange as that
sounds. There's exactly one of them (how could there be different ones?) It's
called "Unit", and looks like `()`. So if you had a function like this:

{% highlight magpie %}
SayHi (->) Print "hi!"
{% endhighlight %}

You could call it like this:

{% highlight magpie %}
SayHi () // prints "hi"
{% endhighlight %}

That's a bit tedious, though, so in most cases you can omit the `()` and
Magpie will infer it:

{% highlight magpie %}
SayHi // prints "hi"
{% endhighlight %}

## What About Returns? I Don't See Print Returning Anything.

Sure, it is. Just like Unit can be omitted as an argument, it's also omitted
as a return. `Print` returns `()` every time it's called. Since a function
returns the result of evaluating its body, that means `SayHi` also returns
`()`.

## What's The Point?

What that gives us is the ability to make everything in Magpie an expression:
since you can return Unit like a value, Magpie doesn't need to make a
distinction between *expressions* (which return things) and *statements*
(which don't). This means that things like flow control can be regular
expressions in Magpie. For example:

{% highlight magpie %}
Square (if 1 < 2 then 3 else 4)
{% endhighlight %}

This calls the `Square` function and passes in 3. We can do this because
`if/then/else` is a regular expression and can be used anywhere.

So having "nothing" be a returnable value lets us make the language a lot more
flexible.

## You Still Forgot One Case&hellip;

And, of course, this also means you can return *multiple* values, just like
you can in Python or Lua:

{% highlight magpie %}
Swap (a Int, b Int -> (Int, Int)) (b, a)

Swap (1, 2) // returns (2, 1)
{% endhighlight %}

So, in the end, it isn't much of a limitation at all.
