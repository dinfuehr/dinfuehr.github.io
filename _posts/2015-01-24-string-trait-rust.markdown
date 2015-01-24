---
layout: post
title:  "String trait in Rust"
date:   2015-01-24 18:12:13
categories: rust programming
---

When starting with Rust I stumbled across a strange error, which I wanted to share. At first I had the simple struct `Foo`:

{% highlight rust %}
struct Foo {
    value: String
}
{% endhighlight rust %}

To print this value with `println!`, I needed to implement the `String` trait for `Foo`:

{% highlight rust %}
use std::fmt::{String,Formatter,Error};

struct Foo {
    value: String
}

impl String for Foo {
    fn fmt(&self, f: &mut Formatter) -> Result<(), Error> {
        write!(f, "value = {}", self.value)
    }
}

fn main() {
    println!("foo = {}", Foo { value: "abc".to_string() });
}
{% endhighlight rust %}

Can you spot the error? I got the the following error message:

<pre>
string.rs:4:12: 4:18 error: explicit lifetime bound required
string.rs:4     value: String
</pre>

I know that I may need to specify lifetimes for e.g. `&str`, but a lifetime for `String` did not really make sense to me. After searching for this error on the internet, I read that this error message is thrown if an element of the struct is a trait. At first I didn't understand that this was the solution, after all I was using a concrete type and not a trait. But was I? Turns out I wasn't and it took me some minutes to find this out. The `use` on line 1 made the `String` trait available, `String` now means `std::fmt::String` and not `std::string::String` any more.

With this knowledge the error was easy to fix. Unfortunately it took me some time to figure that out, but that's just the way it is, when you are learning something new.

The `Show` trait in Rust is now `Debug`, while `String` got renamed to `Display`. This is a good decision for me since I always had trouble to remember when to use `Show` vs `String`. With the new names I don't have this problem any more and I wouldn't have gotten into this whole problem at all. There is also an open issue for the Rust compiler to add some suggestions to the compiler error shown above at [Github](https://github.com/rust-lang/rust/issues/16948).
