---
layout: post
author: Nulliver
title: Rust's Owners
---

Programming languages need to define a way to manage memory (even if it's manual) and Rust is not the exception. The way Rust decided to approach this is through the concept of **ownership**.

There are two places in memory where a Rust program can store data: The **Stack** and **The Heap**. 

To simplify, I'll say that The Stack is for small amounts of data that can be accessed really fast. Most languages handle this pretty straight forward, you usually don't even have to think about it but the most interesting part is how each language handles the data stored in The Heap.

The Heap is the space in memory where the most complex data is stored. The way Rust handle this is by binding the variable to the memory location where data is in The Heap. 

{% highlight rust %}
let data1 = String::from("A more complex object in The Heap");
{% endhighlight %}

In the code above, the variable `data1` is bound to the string data _"A more complex object in The Heap"_. A string is data complex enough that it needs to be stored in The Heap. At this point `data1` becomes the owner of that string. If `data1` goes out of scope then the memory is (in Rust terms) "_dropped_", which means that we cannot longer reach that string through the `data1` variable and that particular memory location is now available to be used by other code or programs.

Now let's consider the following scenario:
{% highlight rust %}
let data2: String; // (A)

{
    let data1 = String::from("A more complex object in The Heap"); // (B)
    println!("{}", data1); // (C)
    data2 = data1; // (D)
    // (D.1) This following line is an error, data1 is now invalid
    //println!("{}", data1);
}

// (D.2) This is also an error, data1 is now out of scope
//println!("{}", data1);

println!("{}", data2); // (E)
{% endhighlight %}

In the code above `data2` is declared at (A) and after that, inside a block, `data1` is declared and bound to some string data in (B). In the next line (C) the value of `data1` is printed to the standard output and then we bound `data2` to the same string `data1` is bound to (D). We have to understand that we didn't make a copy of the string `data1` is bound to, instead we now have the `data2` variable bound to the same string `data1` was prerviously bound to ( _"A more complex object in The Heap"_).

After the block is closed, `data1` is out of scope, we no longer have access to the string using `data1` but we can continue accessing that string using `data2` (E). In fact, `data2` became the owner of that string not after `data1` went out of scope but when we did the assignment of `data1` to `data2` in (D). This is known as a **move** in Rust terms because we're moving the ownership from `data1` to `data2`. 

Even at (D.1) when `data1` was still in scope, we couldn't use it becase Rust invalidated it since we moved the ownership to `data2`. Once we do an assignment, we're changing the owner and the previous owner is invalidated.

A move also occurs when passing a variable to a function or returning it from a function.

{% highlight rust %}
fn main() {
	let data1 = first_owner(); // (A)
	second_owner(data1); // (D)
} 

fn first_owner() -> String {
    let new_data = String::from("New Data"); // (B)
    new_data // (C)
}

fn second_owner(more_data: String) {
    // do something with more_data
}
{% endhighlight %}

In (A) we called the `first_owner()` function, inside that function we bound the variable `new_data` to a string (B). At this point the variable `new_data` is the owner of that string but in the next line (C) we moved the owner to the variable `data1` by returning the string from the function.

In (D) we again moved the owner to inside the `second_owner()` function by passing the variable `data1` to the function.

But what if we don't want to "move" the owner and we really want to make a copy of the data? we use the `clone()` method:

{% highlight rust %}
let data1 = String::from("A more complex object in The Heap");
let data2 = data1.clone();
{% endhighlight %}

This way, we get two variables bound to its own copy of the data.

Now, what if instead of moving or copying the data we want to use it inside a function just for a quick operation? what if we don't really want to "move" the ownership to some variable inside the function?. 

In that case, there's a concept called "borrowing", it works like this:

{% highlight rust %}
fn main() {
	let data1 = String::from("Borrowing data") // (A)
	borrow_for_a_second(data1); // (B)
	println!("We still can use data1 = {}", data1); (D)
} 

fn borrow_for_a_second(borrowed: &String) {
    let length = borrow.len();
    println!("String is {} long", length);
}

{% endhighlight %}

In (A) we bound `data1` to a new string and in (B) we pass that to the function `borrow_for_a_second()` but we're not moving the ownership of the data. By using `&String` as the data type we're telling Rust that we just want to reference the data for a moment and that we don't want to take ownership of the data. After we use the data, we don't have to "move the ownership back" outside the function because we never took ownership of it.

Note that in the function we cannot modify the data, we can only read it. If we want to modify the data through the reference, we have to annotate it with `mut` like this:

{% highlight rust %}
fn borrow_for_a_second(borrowed: &mut String) {
    borrow.push_str(" and modifying it.")
}

{% endhighlight %}

We are now modifying the string by using a mutable reference, we are not the owners but we _borrow_ the data to modify it. Think in this as borrowing a notebook from a friend to take some notes during a class, you use a couple of pages before returning it to your friend, you modified your friend's notebook but you are not the owner.

In conlusion, the rule is that we can have any number of immutable references as long as we don't have a mutable reference. If we want a mutable reference then we cannot have any immutable references.