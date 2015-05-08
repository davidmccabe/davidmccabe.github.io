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



{% highlight javascript %}
function () {

}
{% endhighlight %}
