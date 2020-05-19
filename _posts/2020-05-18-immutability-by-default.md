---
layout: post
author: Nulliver
title: Immutability by default
---
In Rust, variables are immutable by default.

This means that the most common way to declare a variable makes it immutable which is a good thing because you won't change a variable without consciously knowing that you're changing that variable. 

If you declare the following variable:

{% highlight rust %}
let myVariable = 22;
{% endhighlight %}

you cannot change it without getting an error from the compiler:
{% highlight rust %}
myVariable = 55; // the compiler is going to complain about this
{% endhighlight %}

So at this point the compiler forces you to ask yourself "do I really want to change this variable or am I making a mistake?". To make the variable mutable you have to go back to the declaration and add the annotation _mut_:

{% highlight rust %}
let mut myVariable = 22;
{% endhighlight %}

I've seen similar rules in other modern languages like Swift and Kotlin and I think it's becoming the norm because it really help us to detect or avoid errors as early as possible.