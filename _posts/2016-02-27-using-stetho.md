---
title:  "Debugging with Stetho"
author: karthik
date:   2016-02-27 00:18:23 +0700
categories: [Android]
tags: [Android]
---
Stetho is facebook open sourced debug tool that brings in `chrome developer features` natively for android applications with the help of okhttp interceptors.

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/stetho-network.gif)
![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/stetho-inspect1.gif)

## Getting started
To get started add dependency in your gradle file and intialize stetho in the onCreate method of your application class.


## Adding dependency:
 {% highlight grovvy %}
   compile 'com.facebook.stetho:stetho-urlconnection:1.3.1'
 {% endhighlight %}


## Code changes:
 {% highlight java %}
  Stetho.initialize(Stetho.newInitializerBuilder(mContext)
   .enableDumpapp(Stetho.defaultDumperPluginsProvider(mContext))
   .enableWebKitInspector(Stetho.defaultInspectorModulesProvider(mContext))
   .build());
 {% endhighlight %}


{% highlight java %}
OkHttpClient client =  new OkHttpClient.Builder()
                  .addNetworkInterceptor(new StethoInterceptor())
                  .build();
{% endhighlight %}

**Side notes:**
(i)If you have never used okHttpClient,enqueue callback returns the response in  `background thread`, make sure you communicate to the main thread,i have used handler for the purpose.<br/>
(ii)If you are using Picasso to display the images in your application make sure you use the same okhttp instance that has StethoInterceptor configured in it.
personally i would recommend using [Singleton pattern](https://en.wikipedia.org/wiki/Singleton_pattern) to get instance of okhttp.

{% highlight java %}
	picasso = new Picasso.Builder(mContext)
                .downloader(new OkHttp3Downloader(okHttpClient))
                .build();
{% endhighlight %}
make a note that i have used `OkHttp3Downloader`.  

(iii)Do not configure okhttp in the following way
{% highlight java %}
	OkHttpClient client = new OkHttpClient();
	client.networkInterceptors().add(new StethoInterceptor())
{% endhighlight %}


>in okhttp3 the networkInterceptors list is made [unmodifiableList](http://www.tutorialspoint.com/java/util/collections_unmodifiablelist.htm).
hence will throw  `unsupported Operation Exception`.
{: .prompt-warning }


The `video` gives the quickoverview on how stetho helps in monitoring network calls, and inspecting elements in the screens.

<iframe width="420" height="315" src="https://www.youtube.com/embed/gscgCjhRWPk" frameborder="0" allowfullscreen></iframe>

## Notes
 You can additionally view the shared preferences value and the database values using the stetho which i have not included in the video.<br/>
 In the sample application i have created two dummy methods.`fillDummyValuesInSharedPreferences` and `fillDummyValuesInDB` which fills value in SharedPreferences.
 `Realm db` contents will not be shown,while shared preferences will be shown under `local storage` option, and also note that stetho also provides `custom dumpapp plugins`, which i have not covered in this blog post.

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/stethodb.png)

## More Reading:
1.Also there is awesome screencast by Donn felker called [caster.io](https://caster.io/episodes/episode-4-debugging-android-with-stetho/).<br/>
2.Do checkout the sample code in the [stetho repo](https://github.com/facebook/stetho).<br/>
