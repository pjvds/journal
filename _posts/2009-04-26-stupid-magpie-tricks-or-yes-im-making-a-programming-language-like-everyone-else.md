---
layout: post
title: "Stupid Magpie Tricks (Or: Yes, I'm Making a Programming Language Like Everyone Else)"
categories: code language magpie
---
This blog has been collecting dust for several months now. Not because I
haven't been doing anything, quite the opposite. I've been spending all my
free time hacking on a little programming language project: Magpie.

Yesterday, I finally [put it online where people can get to it](http://bitbucket.org/munificent/magpie/). Feel free
to have a look, although it's got a good ways to go before I'd describe it as
really useful.

<blockquote class="update">
<p><em>Update 2011/08/16:</em> Magpie lives in <a href="http://github.com/munificent/magpie">github</a> now.</p>
</blockquote>

I hate "general announcement" blog posts, especially on my blog because it's
not like people read this just to find out what *I'm* doing, so to try to make
this at least a little bit interesting, here's some stupid Magpie tricks:
things that are doable or easy in Magpie that can be a pain in other
languages.

## Constructor Function References

In Magpie, almost everything is a function, including constructors. Since it
doesn't have an explicit `new` keyword, you can pass around a reference to a
constructor like you can any other function.

{% highlight magpie %}
// define a type
struct Point
    X Int
    Y Int
end

Main (->)
    // pass the constructor to a function
    TakeRef fn Point (Int, Int)
end

TakeRef (func fn (Int, Int -> Point))
    // call the ref
    def point <- func (1, 2)

    Print (point.X.String + ", " + point.Y.String)
end
{% endhighlight %}

Try *that* C# and C++!

## Tupled Arguments

Functions in Magpie always take a single argument. To pass in multiple
arguments, you use a tuple. Syntactically, it looks the same as other
languages, but does have an interesting side-effect: you treat the entire
batch of arguments as a single value to be played with:

{% highlight magpie %}
// define a function that takes three args
Sum (a Int, b Int, c Int -> Int) a + b + c

Main (->)
    // calling it looks pretty normal...
    def a <- Sum (1, 2, 3)

    // but you can also do this
    def arg1 <- (1, 2, 3)
    def sum1 <- Sum arg1
    Print sum1.String

    // or this
    def arg2 <- GetArgs
    def sum2 <- Sum arg2
    Print sum2.String
end

// this function returns a tuple
GetArgs (-> (Int, Int, Int)) (4, 5, 6)
{% endhighlight %}

## Is That It?

I know, I know. Not exactly mind-blowing. There's still lots of work for me to
do on the language, but it's getting there. At the very least, there's a
pretty readable hand-coded lexer and LL parser in there.
