---
title:  "Using static code analysis tools"
author: karthik
date:   2016-02-29 00:18:23 +0700
categories: [Android]
tags: [Android]
---

This blog post talks about using `Infer` and `Lint` code analysis tools in android applications,to avoid potential pitfals.

## Facebook Infer
Facebook Infer for android reports potential  `null pointer exceptions` and `resource leaks` in Android and Java code.
Checkout the get started page to setup [infer](http://fbinfer.com/docs/getting-started.html) environment.

>Please make sure you have installed all the dependencies required  `./build-infer.sh` script should return with no issues found therefore compiling the infer.
{: .prompt-info }

Once the infer is compiled and ready, download the gradle wrapper by running  `./gradlew clean` , now use `infer   --- ./gradlew build`.

But i personally make use of  `infer ----debug ---- ./gradlew build` that outputs the additional debug informations such as possible Class Cast Exception etc and generates html pages for each file about analysis done.
Also in the build.gradle add lintOptions so that it will stop gradle build in case of any errors found.

{% highlight groovy %}
android {
	lintOptions{
		abortOnError false
	}
}
{% endhighlight %}

## Protip
note that if your running infer multiple times use `infer -- incremental ./gradlew build`.

## TroubleShooting
If you get error saying infer command not found re-compile the infer using `./build-infer.sh` and then try again.
In case the error still persists then infer is not compiled properly, this might be due to the reason that all dependencies are not installed in your computer.
After a successful Infer run,infer-out a directory is created to store the results of the analysis.

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/infer.png)

**Structure of infer-out:**<br/>
**1.Procs.csv and stats.json:** contain debug information about the run.<br/>
**2.Bugs.txt, report.csv, and report.json:** contain the Infer reports in three different formats.<br/>
**3.The log/, multicore/, and sources/ folders:** are used internally to drive the analyzer.<br/>
**4.Captured:** contains information for each file analyzed by Infer(also see the html pages with detailed analysis if infer is used with  `debug` option).<br/>

Checkout my [sample app](https://github.com/callmekarthik/Playground-App) that has infer-out folder along with the html pages with detailed analysis inside captured folder.

## Android Lint
This tool checks your Android project source files for potential bugs and optimization improvements for correctness.
This is inbuilt tool in Android Studio, and it automatically runs once you compile the code,you can also run it manually by right clicking in the IDE **Analyze > Inspect Code**, moreover you can also analyze dependencies, cyclic dependencies etc.
You can also invoke this through command line.
`$ ./gradlew lint` (in linux and osx).

And the Lint results will be a html page in the  `mobile/build/outputs/lint-results.html`.
i personally feel lint in a way is more advanced and consider's more paramters into the account while dumping the results in the  `lint-results.html`.

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/lint.png)

The optimization tips in the lint-results.html will take all the paramters(such as Security,Performance,Overdraw,Unused resources etc) into the consideration.

## More Reading:
1.For more details about the [Lint](http://developer.android.com/tools/help/lint.html) visit the developer website.<br/>
2.You can also build  `Custom Lint` checks if you think that built-in Lint checks are not enough,get started by visiting this [blog post](https://lab.getbase.com/custom-lint-checks-part-1/).<br/>
