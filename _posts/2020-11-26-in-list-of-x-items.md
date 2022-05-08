---
title:  "In list of X items accessibility issue in recycler view"
author: karthik
date:   2020-11-26 00:18:23 +0700
categories: [Android,Accessibility]
tags: [Android,Accessibility]
---
 If you have enabled accessibility for your application, then you might have noticed first time
 you navigate to recycler view, your talk back might announce the contents of the recycler row along with the additional info of `in the list of x items`.

 If you are like me ever wondering on how we can get rid of it, there is a small hack that you can apply in your `LayoutManager` you need to override a method that is responsible `getRowCountForAccessibility` and `return 0`.

 PS: not that proud of the solution but "it works"

## Reference:
 [RecyclerView.java](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:recyclerview/recyclerview/src/main/java/androidx/recyclerview/widget/RecyclerView.java;drc=f3dbf318fc72bb539a863686c15991c9443732d9;l=10676)
