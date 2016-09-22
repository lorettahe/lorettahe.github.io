---
layout: post
title: "Clojure automatic curry"
description: "Automatic curry"
category: Clojure
tags: [Clojure, macro, curry]
---
{% include JB/setup %}

In Clojure, if you define a function like this:

{% highlight clojure %}
(defn f
  [a b c]
  {:a a :b b :c})
{% endhighlight %}

And call it with

{% highlight clojure %}
(f 1)
{% endhighlight %}

Clojure will tell you that you are being very naughty, and that you have not supplied the right number of parameters to the function f. Indeed, it does have every right to say so. After all, you told f to expect three parameters, and you have only supplied one.

However, in a more traditional functional programming sense, what one might mean by saying **(f 1)** is 'I have only supplied f with one parameter, but I will supply it later in usage'. This is a somewhat very loose explanation of [Currying](https://en.wikipedia.org/wiki/Currying).

Now Clojure doesn't do this automatically, as we have seen above. But Clojure does have the idea of partials, which is pretty similar. Can we write a macro to generate partials automatically for a function? Yes, we can.

{% highlight clojure %}
(defmacro defcurry
  [fname args & body]
  (let [partials (map (fn [n] `(~(subvec args 0 n) (partial ~fname ~@(take n args)) ))
                      (range 1 (count args)))]
    `(defn ~fname
       (~args ~@body)
       ~@partials)))
{% endhighlight %}

That's pretty much what it takes. Now you can write an auto-currying function like this:

{% highlight clojure %}
(defcurry f
  [a b c]
  {:a a :b b :c c})
{% endhighlight %}

Note how this is exactly the same syntax as using the familiar **defn**.

But now, with this function, you can do:

{% highlight clojure %}
(((f 1) 2) 3)
{% endhighlight %}

This will return **{:a 1 :b 2 :c 3}** as expected. How does this work? The call to **defcurry** above generates the following code to be run:

{% highlight clojure %}
(defn f
  ([a b c] {:a a :b b :c c})
  ([a] (partial f a))
  ([a b] (partial f a b)))
{% endhighlight %}

Simples. :)
