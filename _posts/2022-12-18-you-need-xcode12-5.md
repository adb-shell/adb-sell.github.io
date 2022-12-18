---
title:  "You need Xcode 12.5"
author: karthik
date:   2022-12-18 00:18:23 +0700
categories: [Xcode]
tags: [Kotlin Multiplatform]
---
Ever-since JetBrains announced that kotlin multi-platform is in [beta](https://blog.jetbrains.com/kotlin/2022/10/kmm-beta/) I wanted personally explore the multi-platform thing. They have really awesome get started guide, and incase you happen to have latest MacBook laptop then everything would play out smoothly.

But incase you're like me, trying to get max out of a 10 year old personal MacBook pro, then you're in for a disappointment.


1. Environment analysis tool [kdoctor](https://github.com/Kotlin/kdoctor) won't work for you, it's not a blocker, you can always go ahead and verify the Environment manually and if your a mobile developer then this Environment are already set in your laptop.

2. The most **biggest** drawback is that you need, whenever you try to run the iOS code either in Android studio or via Xcode the build would specifically a task called `compilekotliniosx64` would fail, and the error message would be as follows `Compilation failed due to unsupported Xcode version, min required is 12.5`

And unfortunately if mac OS is below 11.0(Big Sur), you will no longer able to install Xcode 12.5, hence unable to run your iOS app, thus you can try your Multi-platform app for Desktop and Android OS alone.


## Small caveat:
All though you can try your luck by downgrading the kotlin Multi-platform plugin version, kotlin compiler version, AS version along with older Xcode version to get it working, **but I personally don't think the investment is worth it!**.

## Reference doc:
[https://github.com/JetBrains/kotlin/blob/master/kotlin-native/RELEASE_NOTES.md](https://github.com/JetBrains/kotlin/blob/master/kotlin-native/RELEASE_NOTES.md) under Compatibility and features you will see ** macOS it also requires Xcode 12.5 or newer to be installed.**
