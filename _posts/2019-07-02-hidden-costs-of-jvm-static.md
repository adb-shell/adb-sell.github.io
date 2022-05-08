---
title:  "Hidden costs of using @JvmStatic"
author: karthik
date:   2019-07-02 00:18:23 +0700
categories: [Kotlin]
tags: [Kotlin]
---
Underling hidden costs of using JvmStatic in kotlin.


Consider we have a `class A` in kotlin, and we use static factory pattern to **getInstance** of class A.

{% highlight kotlin %}
class A{
  companion object{
      @JvmStatic
      fun getInstance():A = A()
  }
}
{% endhighlight %}


Now the only reason we `@JvmStatic` is when we have inter-op from java, where an instance of class A is called from java.
Let's look into underlying java code thats generated when we use `@JvmStatic` for class A

{% highlight java %}

public final class A {
   public static final A.Companion Companion = new A.Companion((DefaultConstructorMarker)null);

   @JvmStatic
   @NotNull
   public static final A getInstance() {
      return Companion.getInstance();
   }

   @Metadata(
      mv = {1, 1, 15},
      bv = {1, 0, 3},
      k = 1,
      d1 = {"\u0000\u0012\n\u0002\u0018\u0002\n\u0002\u0010\u0000\n\u0002\b\u0002\n\u0002\u0018\u0002\n\u0000\b\u0086\u0003\u0018\u00002\u00020\u0001B\u0007\b\u0002¢\u0006\u0002\u0010\u0002J\b\u0010\u0003\u001a\u00020\u0004H\u0007¨\u0006\u0005"},
      d2 = {"Lproblems/Arrays/A$Companion;", "", "()V", "getInstance", "Lproblems/Arrays/A;", "kotlin-exercises"}
   )
   public static final class Companion {
      @JvmStatic
      @NotNull
      public final A getInstance() {
         return new A();
      }

      private Companion() {
      }

      // $FF: synthetic method
      public Companion(DefaultConstructorMarker $constructor_marker) {
         this();
      }
   }
}

{% endhighlight %}


Please note there is an extra method `getInstance` in the outer `class A`.


Now when we use it without `@JvmStatic` following code gets generated.

{% highlight java %}

public final class A {
   public static final A.Companion Companion = new A.Companion((DefaultConstructorMarker)null);

   @Metadata(
      mv = {1, 1, 15},
      bv = {1, 0, 3},
      k = 1,
      d1 = {"\u0000\u0012\n\u0002\u0018\u0002\n\u0002\u0010\u0000\n\u0002\b\u0002\n\u0002\u0018\u0002\n\u0000\b\u0086\u0003\u0018\u00002\u00020\u0001B\u0007\b\u0002¢\u0006\u0002\u0010\u0002J\u0006\u0010\u0003\u001a\u00020\u0004¨\u0006\u0005"},
      d2 = {"Lproblems/Arrays/A$Companion;", "", "()V", "getInstance", "Lproblems/Arrays/A;", "kotlin-exercises"}
   )
   public static final class Companion {
      @NotNull
      public final A getInstance() {
         return new A();
      }

      private Companion() {
      }

      // $FF: synthetic method
      public Companion(DefaultConstructorMarker $constructor_marker) {
         this();
      }
   }
}

{% endhighlight %}

Notice the absence of the `getInstance` in the outer class. In case we don't use `@JvmStatic` from java getting instance of `class A` would be with one extra `Companion` as shown below.

{% highlight java %}
 A.Companion.getInstance()
{% endhighlight %}

>Now given the limitation of only `65,536` method counts in a single dex (for android), using `@JvmStatic` just to reduce `Companion` keyword is **costly affair in Android**.
{: .prompt-info }

## More reading:
1.[@JvmStatic](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-static/index.html)<br/>
2.[When and Why to use @JvmStatic](https://stackoverflow.com/questions/48780003/why-and-when-to-use-jvmstatic-with-companion-objects).
