---
title:  "Annotation Processor vs AOP."
author: karthik
date:   2018-12-17 00:18:23 +0700
categories: [Programming]
tags: [Programming]
---

Blog post on when to use @processor vs aop(aspect oriented programming).

Question here boils down whether you need to get rid of boiler plate code or have a responsibility(logging, method tracing) shared across classes that you need to execute without `polluting` classes(I would hate it if a method has 15 lines of code out of which 10 lines are just used for logging/method tracing).

# @Processor:
Mainly used to generate code, without modifying the existing code behaviour, this was introduced mainly to eliminate boiler plate codes. The main advantage you get here is `you just have to test the generator` the generated code(boiler plate) will be error free for any number of class's you use them on, Just like `@singleton` in Dagger.
If your keen on how to build one, then i suggest you to go through Ryan's excellent [video](https://academy.realm.io/posts/360andev-ryan-harter-eliminate-boilerplate/) and then Mert Şimşek's [medium article](https://medium.com/@iammert/annotation-processing-dont-repeat-yourself-generate-your-code-8425e60c6657).

If your into android there are pretty famous libraries that use annotation processor, such as [Butterknife](https://github.com/JakeWharton/butterknife), [FragmentArgs](https://github.com/sockeqwe/fragmentargs) and a more complicated one [Dagger](https://github.com/google/dagger)


## AOP(Aspect oriented programming):
Useful technique that enables adding executable blocks to the source code without explicitly changing it.This approach useful when you have responsibility(or a concern) that has to be shared across the floor,(In case of MVP all models, views and presenters share this, for instance logging, persistence etc) such a shared concern(responsibility) we call it as `cross cutting concern`.such concerns should not `pollute` our method but instead should happen behind the scenes, we can modularise such concern(s) in a class and annotate it with `Aspect`, this aspect is then injected to our methods by the `aspectj compiler (aka ajc )` during the compile time.

## How ,Where,When and What:
1.**How:** to inject is taken care by ajc, the process is called weaving.

2.**Where:** to inject is defined by us, it is similar to a regular expression as shown `execution(@com.karthik.pretty_annotation.Pretty * *(..))` which basically states inject it any method with visibility modifier *(includes private, protected, default and public) whose arguments are * (can be anything) but annotated with @Pretty.This expression usually called as `pointcut expression`.

3.**When:** to inject is defined by three sets of annotation named `before` ,`after` and `around` typically we use it with pointcut expression `@Around(execution(@com.karthik.pretty_annotation.Pretty * *(..)))` as the name suggests it can be `before` method is executed or `after` or instead of / along with method execution (around). usually called `advice`

4.**What** to inject is typically a cross cutting concern, such as logging, tracing etc.

as summarised in the example below.

{% highlight java %}

     @Aspect
     public class MethodLogger {
       @Around("execution(@com.karthik.pretty_annotation.Pretty * *(..))")
       public Object around(ProceedingJoinPoint point) {

          //cross cutting concern code is written here

          //proceed with original method execution
          return point.proceed()
       }
     }

{% endhighlight %}


## More resources on AOP<br/>
1.[Java Method Logging with AOP](https://www.yegor256.com/2014/06/01/aop-aspectj-java-method-logging.html).<br/>
2.[AOP in Android](https://fernandocejas.com/2014/08/03/aspect-oriented-programming-in-android/)
