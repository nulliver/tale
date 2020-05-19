---
layout: post
author: Nulliver
title: Constants and immutability
---
Yesterday I mentioned that in Rust variables are immutable by default.

So the next question is "Does Rust have constants? if so, what role do they play?" 

Yes, Rust has constants and they can be declared like this:

{% highlight rust %}
const NEVER_CHANGE: u32 = 999;
{% endhighlight %}

The differences between constants and variables go beyond immutability:

* Variables are immutable by default but they can become mutable by annotatting them with the _mut_ keyword. Constants are **always** immutable, you cannot annotate a constant with _mut_.
* You declare constants using the _const_ keyword.
* Constants must be annotated with a type (u32 in the example above).
* Constants can be declared in any scope, including the global scope.
* The value of the constant must come from a constant expression, it cannot come from a computation determined at runtime.

By convention, constant are declared using all uppercase letters and underscores to separate words, the compiler won't complain if you use a different convention but other _Rustaceans_ will ("Rustaceans" is a silly name Rust developers call themselves).