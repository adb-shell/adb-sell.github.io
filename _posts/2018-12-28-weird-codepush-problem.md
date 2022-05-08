---
title:  "Singleton ReactInstance Manager"
author: karthik
date:   2018-12-28 00:18:23 +0700
categories: [React-Native]
tags: [React-Native]
---

Weird code-push issue with associated with multiple instances of `ReactInstanceManager` class in Android.

Recently, I was working on a project which had part of its screen's implemented in `react-native`(other parts were native). After developing a new screen which was in react-native, we encountered a strange problem with code-push.

Each time you do a code-push, it would rollback to the previous version. At first we debugged our JS code for any potential errors that would have caused this, but found none.

After hours of scratching our head, we found out that **ReactInstanceManager class needs to be Singleton**(in android), which we missed in react-native documentation.

>A ReactInstanceManager can be shared by multiple activities and/or fragments. You will want to make your own ReactFragment or ReactActivity and have a singleton holder that holds a ReactInstanceManager. When you need the ReactInstanceManager (e.g., to hook up the ReactInstanceManager to the lifecycle of those Activities or Fragments).
{: .prompt-info }

So each time you create new instance ReactInstanceManager, we create new instances of `CodePush`, which results in hash mismatch between hash-key obtained and folder hash(I will dig into the source code more, and update the post, stay tuned!!).
