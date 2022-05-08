---
title:  "ClipToPadding attribute to rescue."
author: karthik
date:   2018-08-24 00:18:23 +0700
categories: [Android]
tags: [Android]
---

## Introduction
By setting ClipToPadding = false , **The view draws beyond its bounds into the padding area, overwrite this drawing with the parent's padding**.

By default Android sets this property to `true` for any ViewGroup, which means they will be clipped to adjust to the padding of the ViewGroup.

Below images depicts better than any documentation.

## In case of RecyclerView:

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/recyclerview.gif)
<sub><sup>*image credits pulpfiction.</sup></sub>

**In case of Fab button:**

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/fab.png)

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/cliptrue.png)
clipToPadding = false
 The shadow is drawn evenly, even if it exceeds the view bounds.


![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/clipfalse.png)
Whereas clipToPadding = true
 The shadow is clipped when it crosses the view bounds.<br/>
<sub><sup>*image credits Samuel Peter.</sup></sub>


Overall setting this property to `false`, in most cases leads to better UX.


## More Reading:

1.[StackOverflow    answer](https://stackoverflow.com/questions/38970637/clarification-about-android-cliptopadding-property)

2.[Android doc](https://developer.android.com/reference/android/view/ViewGroup#attr_android:clipToPadding)
