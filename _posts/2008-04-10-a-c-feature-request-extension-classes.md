---
layout: post
title: "A C# Feature Request: Extension Classes"
categories: c-sharp code
---
As you [may have noticed](http://journal.stuffwithstuff.com/2008/02/09/c-extension-methods-not-just-for-breakfast/), I'm a big fan of [extension methods](http://msdn2.microsoft.com/en-us/library/bb383977.aspx) in C#
3.0, but they aren't without their limitations. The first one you notice when
you use them is the [lack](http://weblogs.asp.net/scottgu/archive/2007/03/13/new-orcas-language-feature-extension-methods.aspx#2019379) of extension properties. Since I like
properties more than I like extension methods, this really bummed me out. So
for kicks, I'd like to propose a C# feature request: **Extension classes**.

## What's An Extension Class?

An extension class is a class that adds methods, properties, etc. to another
class. Given some class `Foo`:

{% highlight csharp %}
public class Foo
{
    public int Value { get { return 3; } }
}
{% endhighlight %}

My proposed syntax is:

{% highlight csharp %}
public class FooExtensions this Foo
{
    public int DoubledValue
    {
        get { return Value * 2; }
    }

    public void Hi()
    {
        Console.WriteLine("hi.");
    }
}
{% endhighlight %}

The magic bit up there is the "`this Foo`". That basically says, "the '`this`'
reference inside this class is actually of this other type '`Foo`'". As you
can see in the implementation of `DoubledValue`, it's accessing the `Value`
property, which is defined in `Foo`.

So, this solves the syntax problem for how to define an extension property. It
makes sense, too, because if you've used extension methods, you've noticed
that you always have to define them inside a "special" extension class anyway.
This just moves the "extension-ness" indicator from the method level up to the
containing class level.

## What It's Not

Just to be clear, this *isn't* the same as inheriting, partial classes, or
open classes. There is no way to have a reference of type `FooExtensions`,
because that class is not directly instantiable. It's just decorating another
class. Likewise, `FooExtensions` does *not* have priveleged access to `Foo`.
It can only use public members just like outside code. And, of course,
`FooExtensions` can't replace any methods in Foo. No open classes.

## And Then?

OK, so we've got properties in the mix, is there anything else we could add?

{% highlight csharp %}
public class FooExtensions this Foo
{
    public static SomeMethod() { /* ... */ }
}
{% endhighlight %}

How about static methods and properties? You could say there's no value in
doing this since you have to access statics through the class name anyway, but
I disagree.

A design guideline that's been floating around in [C++ land](http://www.amazon.com/dp/0201924889) for a long
time is "[prefer non-member functions over member functions](http://www.aristeia.com/effective-c++_frames.html)". The idea is
that coupling and priveleged access to a class are bad, so if you can
implement something outside of the class, that's better. However, if doing
that changes the calling convention, you've affected all your call sites with
what should be an implementation detail. I don't want users to have to _know_
that `SomeMethod()` was implemented outside of `Foo`. That way, if I later
need to move it *into* `Foo` (or vice versa), they aren't affected.

## Where This Is Going

So, if you can move more and more *out* of `Foo`, what's left *in* it? The
answer: data and validation. `Foo` itself is the only class that can have
fields. In my world, the way I'd like to design classes is like this: the core
class `Foo` contains its fields and provides accessors and mutators to prevent
it from being put in a bad state. `Foo` basically *is* state and state change.

Almost everything else, the "things you can do with a `Foo`" would be pulled
out into extension classes, likely in different namespaces. If you want to
format a `Foo` for display, `UI.FooExtensions` will have methods for that.
Need to serialize it? `Serialization.FooExtensions`.

Aside from being crazy about decoupling, there's some more reasoning behind
this. It turns out that state is actually one of the [trickiest](http://en.wikipedia.org/wiki/Side_effect_%28computer_science%29) parts of
programming. Computer scientists have been wrestling with its
[implications](http://en.wikipedia.org/wiki/Memoization) since the dawn of programming, and have tried [all
sorts](http://en.wikipedia.org/wiki/Purely_functional) of [ways](http://www.haskell.org/) to get around it, up to the point of trying to
abolish it completely ([sort of](http://www.haskell.org/tutorial/monads.html)) in "pure" functional languages.

Now the lack of real-world use of languages like that kind of hints that state
is inevitable. One of the most useful things about computers is that they
remember things. However, isolating and minimizing *where* and *how* they
remember things, I think, will encourage us to write software that's easier to
[reason about](http://en.wikipedia.org/wiki/Referential_transparency_%28computer_science%29) and maintain.
