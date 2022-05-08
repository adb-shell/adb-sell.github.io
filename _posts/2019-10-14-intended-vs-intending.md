---
title:  "Intended vs Intending in Espresso."
author: karthik
date:   2019-10-14 00:18:23 +0700
categories: [Android,espresso]
tags: [Android,espresso]
---
Test your intents using intended, intending when to use what.

You might be already aware of `Espresso-Intents` which is an extension of Espresso used for testing Intents.The espresso intents basically offers two methods for intent validation.

>Note:<br/> If your not using `IntentsTestRule` but using `ActivityTestRule` or `ActivityScenario`, Please make sure you call `Intents.init()` and `Intents.release()` before and after testing the intents.
{: .prompt-info }


## 1.Intended():<br/>
This method is used to verify that the given `intent` is already seen, which means you cannot use it to test an intent that is going to launch, reason being  **verification does not have to occur in the same order as the intents were sent**(If you have to then try using espresso idling resource).

## 2.Intending():<br/>
The main use case of using intending, is when you start activity for result. If you have ever used [mockito](https://github.com/mockito/mockito) framework, it is similar to `when` that is once you start activity for result then respond with this result.One of the best thing is it can be used to verify external activities like contact picker or camera etc. Intending can be used to verify the launching intent also.


## More reading:
[Espresso-Intents](https://developer.android.com/training/testing/espresso/intents)
