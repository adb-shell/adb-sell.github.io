---
title:  "Weird File-provider error in android"
author: karthik
date:   2020-04-27 00:18:23 +0700
categories: [Android]
tags: [Android]
---

If we look at the be behaviour changes in android-7 [Nougat](https://developer.android.com/about/versions/nougat/android-7.0-changes#sharing-files). You will find file:// URI sharing outside the app is prohibited and may result in `FileUriExposedException`.

This is when `FileProvider` subclass of content provider comes to the rescue, which has a method `getUriForFile()` which returns URI that can be shared outside the app. You follow the standard steps mentioned in [android docs](https://developer.android.com/reference/androidx/core/content/FileProvider) and implement the in your app. So far everything looks fine.

And then when your sharing the URI, if you have used `ShareCompat` like me, you add URI in only one of the two methods `addStream()` or `setStream()` with `Intent.FLAG_GRANT_READ_URI_PERMISSION` flag applied, and then call `createChooserIntent()`
and start activity which brings up the standard Android activity chooser.
However even though you have done everything right, you see a strange log in the logcat.
<br/><br/>

>**java.lang.SecurityException: Permission Denial: reading androidx.core.content.FileProvider uri ..**
{: .prompt-danger }  

At this point you would be tempted to export the file-provider at this point, or run a for loop granting all packages the permission using `context.grantUriPermission()` as below code snippet, which again not a great solution.
{% highlight java %}
List<ResolveInfo> resInfoList = context.getPackageManager().queryIntentActivities(intent,
                                              PackageManager.MATCH_DEFAULT_ONLY);
for (ResolveInfo resolveInfo : resInfoList) {
    String packageName = resolveInfo.activityInfo.packageName;
    context.grantUriPermission(packageName, uri, Intent.FLAG_GRANT_WRITE_URI_PERMISSION | Intent.FLAG_GRANT_READ_URI_PERMISSION);
}
{% endhighlight %}

<br/><br/>
>After lot of RnD i found the answer to the above problem in hidden documentation of [FLAG_GRANT_READ_URI_PERMISSION](https://developer.android.com/reference/android/content/Intent.html#FLAG_GRANT_READ_URI_PERMISSION), which states the permission grants read operations on the URI in the Intent's **data** and any URIs specified in its **ClipData**.
{: .prompt-tip }

If you have observed `ShareCompat` does not expose a direct API to set the `URI` in **data** or **ClipData**, but however you can use `getIntent()` method of ShareCompat and then set `ClipData` accordingly. as shown in the below code snippet.

{% highlight kotlin %}
val sendIntent = ShareCompat.IntentBuilder.from(requireActivity())
          .setType(requireContext().contentResolver.getType(uri))
          .setChooserTitle(title)
          .setSubject(subject)
          .setStream(uri)
          .intent

      sendIntent.apply {
          clipData = ClipData.newRawUri("transactions", uri)
          addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)
      }      
{% endhighlight %}

And then share intent using `Intent.createChooser()`
