---
title:  "Effectively using Lazy"
author: karthik
date:   2019-06-10 00:18:23 +0700
categories: [Kotlin]
tags: [Kotlin]
---
Effectively using property delegation function `lazy` in kotlin.

## Using lazy in kotlin
We all know kotlin's standard delegation function `lazy` which lazily initialises our object, let's say you have condition if this object is initialised you need to either dispose the resources.

Now normally we would do the following
{% highlight kotlin %}

  val resource<T> by lazy<T> {
    //lazy initialisation logic
  }
{% endhighlight %}

and when we need to  dispose resource we would do
{% highlight kotlin %}
  resource.dispose()
{% endhighlight %}

## Catch
But there is catch this would internally run the `lazy initialisation logic` and then dispose the resource, we are unnecessarily initialising and then disposing.

Instead we can use a public method that lazy interface provides, by this we are effectively using the lazy property delegation.

{% highlight kotlin %}
  val  resourceDelegate = lazy { /** resource initialising **/}
  val resource:ResourceType by resourceDelegate
  if(resourceDelegate.isInitialized()){
     resource.dispose()  
  }
{% endhighlight %}


## More reading:
[Lazy interface doc](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-lazy/).
[How lazy-initialization work](https://medium.com/til-kotlin/how-kotlins-delegated-properties-and-lazy-initialization-work-552cbad8be60)
