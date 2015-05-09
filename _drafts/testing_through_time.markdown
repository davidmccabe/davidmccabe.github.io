---
layout: post
title:  "Property Testing React Components"
date:   2015-05-08 11:38:37
categories: react testing
---

In this blog post we'll see how to create thorough, succinct, principled unit tests for React components. With this technique, we can make assertions about the entire timeline of a user interface, rather than simply about its state at any one point in time. For example, we could assert, with one clear line of code, that a particular element never re-appears after disappearing.

It's based on the idea of [simulation testing](http://youtube.com/watch?v=N5HyVUPuU0E), but takes it from the realm of whole systems to the realm of individual React components.

I also gave [a talk about property testing for React.](https://vimeo.com/122070164)

Property Testing In General
--------------------------

Traditionally, we test code using *unit tests*. A unit test is basically an example input paired with an example output:

{% highlight javascript %}
assert( reverse([1,2,3]) === [3,2,1] )
{% endhighlight %}

With property testing, we instead specify *properties of the input* and *properties of the output*:

{% highlight javascript %}
property(array(int), a => reverse(reverse(a)) === a)
{% endhighlight %}

Property Testing a Very Simple React Component
----------------------------------------------

The simplest React components can be considered as functions
from their props to a DOM node. To test them, we need to generate
props and then make assertions about the DOMs they render.

Testing Through Time
--------------------

To test something by property testing, we must ask ourselves what its inputs and outputs are. While the simplest React components can be considered as functions from their props to a DOM, the more interesting React components respond to user input and change over time. So the inputs of a React component are a stream of user inputs and props changes, and the outputs are a stream of DOMs and callbacks (or whatever mechanism you use to let the component communicate with the wider world).

So here are the steps to test a component:

* Generate a series of user events.
* Render the component.
* Apply each event, recording the state of the DOM afterward.
* Make assertions about the recorded history of the DOM.



The Trapdoor Property
-----------------

Let's define a function that tells us whether, at a given step, the list begins with a hyphen:

{% highlight javascript %}
function startsWithHyphen(step) {
	return step.choices.first() === '-'
}
{% endhighlight %}

Now we can assert that, over time, the trapdoor select first has a hyphen, then stops having a hyphen, and then never has a hyphen again:

{% highlight javascript %}
steps.skipWhile(startsWithHyphen)
      .skipWhile(not(startsWithHyphen))
	  .equals(List([]))
{% endhighlight %}

We can generalize this idea:

{% highlight javascript %}
function successively(steps, ...predicates) {
	return List([]).equals(
		steps.reduce(
			(result, predicate) => result.skipWhile(predicate),
			steps,
			predicates))
}

successively(steps, startsWithHyphen, not(startsWithHyphen))
{% endhighlight %}

And, if it's handy, name and reuse the trapdoor property:

{% highlight javascript %}
function trapdoor(steps, predicate) {
	return successively(steps, predicate, not(predicate));
}

// Testing the TrapdoorSelect:
trapdoor(steps, startsWithHyphen)

// Making sure that a system message never
// comes back after being dismissed:
trapdoor(steps, showsSystemMessage)
{% endhighlight %}



Boring Machinery
----------------

How do we generate events and gather steps?
