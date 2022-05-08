---
title:  "Using object keyword in kotlin."
author: karthik
date:   2018-07-13 00:18:23 +0700
categories: [Kotlin]
tags: [Kotlin]
---

## Introduction
In this post, we'll understand the usage of **object** keyword in  kotlin

Object key word in kotlin literally means the `object of the class`, now this keyword can be used in three different scenarios.
1.As a expression.
2.As a declaration.
3.Along with companion keyword.

## 1. As an expression:
This is exactly equivalent of creating anonymous objects in java, syntax is used as `object:` as shown in the below example.
        {% highlight kotlin %}
        val listner = object:Listner(){
                @Override
                fun onSuccess(){

                }
                @Override
                fun onFailure(){

                }
             }
        {% endhighlight %}

unlike java `anonymous` inner classes, can implement interfaces, and can be assigned to a variable, but one thing to note is that each time anonymous class is invoked a new object is created. As shown in the below example

        {% highlight kotlin %}


          fun main(args: Array<String>) {
              abstract class Listner{
                  abstract fun success()
                  abstract fun failure()
              }
             val list = object:Listner(),list{
                override fun success(){
                    println("success")            
                }

                override fun failure(){
                  println("fail")
                }

                override fun suc(){
                  println("suc")
                }
             }
          list.suc()
        }

        interface list{
          fun suc()
        }

        {% endhighlight %}

would print `suc` as its output.

## 2. As a Declaration:
  {% highlight kotlin %}
   object A
  {% endhighlight %}
 here A is singleton in nature, which is equivalent of writing below java code.
     {% highlight java %}
     public class A{
       private A a;
       private A(){

       }
       public static synchronized A getInstance(){
          if(a==null){
            a = new A();
          }
          return a;
       }
     }
     {% endhighlight %}
  since the instance of the class is created immediately, there is no constructor allowed inside object, it can also implement and extend like any other kotlin classes.

## 3. As a companion:
  equivalent to static factory methods in java, unlike static methods in java `companion object` can implement an interface and can have extension functions.
  The only difference between package level function and companion object is that, companion object can access private variables(including the private constructor) of the class containing it.
  If your extending a class, companion objects are not overridden.
  {% highlight kotlin %}

   class Person{
     interface transform{
       fun fromJson(response:String):T
     }
     companion object:transform{
       fromJson(response:String):Person{
         ...
       }
     }
   }

   //can be called as
   Person.fromJson(response)
  {% endhighlight %}
