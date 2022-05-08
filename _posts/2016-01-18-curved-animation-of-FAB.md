---
title:  "Curved animation of FAB"
author: karthik
date:   2016-01-18 00:18:23 +0700
categories: [Android]
tags: [Android]
---
This blog post demonstrates on how to implement curved FAB motion and add circular reveal animation to it.

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/fab1.gif)


## Animations
We will split the animation into arc motion animation and circular reveal animation, the arc motion animation is achived based on the concept of [bezire curve](https://en.wikipedia.org/wiki/B%C3%A9zier_curve).
There is also an awesome sample repo by saulmm that demonstrates curved motion of [fab](https://github.com/saulmm/Curved-Fab-Reveal-Example).<br/>

## Part 1
## Classes
I will give you an overview of few classes that i used from the repo.
**1.PathPoint.java**:<br/>
A class that holds information about a location and how the path should get to that location from the previous path location (if any).
Any PathPoint holds the information for its location as well as the instructions on how to traverse the preceding interval from the previous location.<br/>
**2.AnimatorPath.java**:<br/>
Class that contains the list of pathpoint objects for a given curve,
also specifies type motion either discontinous move to ,straight path or curve to motions of the fab to the given point.<br/>
**3.PathEvaluator.java**:<br/>
This evaluator interpolates between two PathPoint values given the value t, the proportion traveled between those points.
The value of the interpolation depends on the operation specified by the endValue (the operation for the interval between PathPoints is always specified by the end point of that interval).
basically responsible for calculating the cureved bezire path points.<br/>

The android co-ordinate system is such that 0,0 is on the top left corner.
(0,MaxY) is bottom left and (maxx,maxy) bottom right and right top is (Maxx,0).
therefore points for the exponential curve are as follows (-200, -50, -230, -450, -255, -500).

## Code
{% highlight java %}
 	AnimatorPath path = new AnimatorPath();
 	path.moveTo(0, 0);
 	path.curveTo(-200, -50, -230, -450, -255, -500);
 	final ObjectAnimator anim = ObjectAnimator.ofObject(this, "fabLoc",new PathEvaluator(), path.getPoints().toArray());
 	anim.setInterpolator(new AccelerateDecelerateInterpolator());
 	anim.setDuration(300);
 	anim.start();
{% endhighlight %}

Now as the animator produces the new path points we need setter method that set's fab location.
so define this method in your fragment.
This setter will be called by the ObjectAnimator given the  `fabLoc` property string as we did above.

{% highlight java %}
public void setFabLoc(PathPoint newLoc) {
  	mFab.setTranslationX(newLoc.mX);
    //flag describing whether layout is revealed or not
    if (mRevealFlag)
        mFab.setTranslationY(newLoc.mY - (mFabSize / 2));
    else
        mFab.setTranslationY(newLoc.mY);
 }
{% endhighlight %}

## Part 2
  Now the second part  `reveal animations` you have to wait until the curved animation to end.
  Then  call reveal animation, we can add listner which gives arc motion animation end callback.
  on end method of animation listner  create circular reveal animations using the  `ViewAnimationUtils`

  **Note:**<br/>
  please note that `ViewAnimationUtils` available only for android 5.0 and above

  {% highlight java %}
   ViewAnimationUtils.createCircularReveal(revealLayout,cx,cy,0,finalRadius);
  {% endhighlight %}


Now here the tricky part would be to reverse animate along the path
(same points in the reverse order doesnt work, also i was lazy to calculate the points like how we did it to move upwards)
instead extend interpolator and override the method as follows.

{% highlight java %}
@Override
 public float getInterpolation(float paramFloat) {
    return Math.abs(paramFloat -1f);
 }
{% endhighlight %}

## Note:
this kind of fab animation will be useful when you want to animate the fab in the fragment or maybe inside one single activity without transitions.<br/>
In case of the activity transition animation please follow my next blog post where we can achive it using transitions by moving from one activity to another.<br/>

## More Reading:
1.Find out more in detail by [visiting the project on GitHub](https://github.com/callmekarthik/AnimationsDemo).<br/>
2.Do check this old but awesome blog post about curve motion by [Chet Haase](http://graphics-geek.blogspot.com.es/2012/01/curved-motion-in-android.html).<br/>
