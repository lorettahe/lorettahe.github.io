---
layout: post
category: Clojure
title: "Clojure and OO [part 1]"
tagline: "Why and How"
tags: [Clojure, OO]
---
{% include JB/setup %}

## Why?
Some say: 'Isn't Clojure there so we can get away from the OO nonsense? Why would you ever do this?'

Well, java is still there, real and all. What I have found through practice, is that a dynamic functional language isn't *all that good* for putting down a structure for a decently sized project. Being flexible sometimes means you have to sacrifice a bit of robustness.

This is where OO comes in. It's just my personal opinion, but I do think properly structured classes can be a decent backbone for a project, hence I am all for using OO for structure, and filling the inside logic with tasty Clojure. Think of it as a nice beef wellington, if you will. Good crisp layered pastry, with tasty straight-forward beef inside. Yum. 

OK, now let's get on with it, shall we?

##How?

###Interfaces
As far as I know, there are two ways to do this: definterface and defprotocol. They essentially achieve the same thing - build a java interface, but they do have subtle differences:

1. Methods in defprotocol always refer to 'this', i.e. itself as a first parameter, while methods in definterface never refer to 'this' at all:

{% highlight clojure %}
(definterface I (foo []))
;=> user.I
(defprotocol P (foo []))
;=> IllegalArgumentException

(definterface I (foo [this]))
;=> IllegalArgumentException
(defprotcol P (foo [this]))
;=> user.P
{% endhighlight %}
	
2. defprotocol allows you to use the method names by themselves without a '.' in from of it, like how one normally calls a java method, but definterface doesn't.

{% highlight clojure %}
(defrecord IRecord [x] I (foo [this] x))
(defrecord PRecord [x] P (foo [this] x))

(.foo (IRecord. 10))
;=> 10
(.foo (PRecord. 10))
;=> 10
(foo (IRecord. 10))
;=> IllegalArgumentException
(foo (PRecord. 10))
;=> 10
{% endhighlight %}

Other than that, there isn't that much difference. 
	
*But that is not all...*
	
<div class="alert">
  <h4>Warning!</h4>
  One cannot use <strong>varargs</strong> with either of these. This may be improved in the future. Currently you will need to write a separate defn that accepts varargs, and to call the method in the interface/protocol defined without varargs. See below:
</div>

{% highlight clojure %}
(defprotocol P (-many-args [this args]))
(defn many-args [this & args] (-many-args this args))
{% endhighlight %}

I think that now just about sums up defining interfaces in Clojure. I realise it's not much use without solid implementations of them, but this post is already too long, so I will be continuing that in part 2 of the post, which is to come.




