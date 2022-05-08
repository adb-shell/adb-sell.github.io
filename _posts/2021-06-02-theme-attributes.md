---
title:  "Theme attributes in android"
author: karthik
date:   2021-06-02 00:18:23 +0700
categories: [Android]
tags: [Android]
---

## Introduction
Lately i have been facing a weird issue with respect changing themes in android as far i knew, call `setTheme()` before any view creation preferably in the `onCreate(savedInstanceState: Bundle)` method of an activity.
Everything should work as expected, there are some stackoverflow posts suggesting to call before `super` call in onCreate method, i seem to try everything but none seems to work.

There are also suggestions, incase your theme overrides only few attributes then `theme.appleStyle()` API, but still API remained un-fruitful.

## Solution
However i stumbled on the blog post by [Nick Butcher](https://medium.com/androiddevelopers/android-styling-prefer-theme-attributes-412caa748774).
After that only i realised that i had been `referencing the styles directly in the xml`, instead of accessing attributes.

That is

{% highlight xml %}
<TextView
  ...
  android:style="@style/CaptionText.Strong"
  />
{% endhighlight %}

 instead of the above i should have been doing

{% highlight xml %}
 <TextView
   ...
   android:style="?CaptionText.Strong"
   />
 {% endhighlight %}

 In the latter i am trying to access the attributes from currently `setTheme(...)`, which is actually the recommended approach as it makes adopting to new themes much easier.
 Which means tomorrow if you have dark theme with some minor change in `CaptionText.Strong` style, it would easily be reflected once you change the theme.
