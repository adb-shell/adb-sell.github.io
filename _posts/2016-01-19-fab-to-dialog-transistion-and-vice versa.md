---
title: FAB to dialog transistion and vice versa.
author: karthik
date:   2016-01-19 00:18:23 +0700
categories: [Android]
tags: [Android]
---
In this blog post i am  gonna show you how to implement smooth transition between dialog and a fab,similar to Nick Butcher's famous `Plaid app`.

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/fab2.gif)


  Fab to dialog transition is achieved by having transition between two activities.
 `Transistion` as per the doc the definition goes like this

## Definition
 >Activity transitions in material design apps provide visual connections between different states through motion and transformations between common elements.

## Types of transitons
  According to the developer website `shared transistion` animation decides on how the views are shared during transistion.
  Android defines 4 types of transitions<br/>
  **1.changeBounds** : <br/>
  change the layout border target view.<br/>
  **2.changeClipBounds** : <br/>
  border cropped target view.<br/>
  **3.changeTransform** : <br/>
  change the zoom target view scale, and rotation angle.<br/>
  **4.changeImageTransform**: <br/>
  change target picture size and scaling.<br/>

  shared elements should have one additional attribute   `android:transitionName = ""`
  so before we dive into animation,let's have a brief introduction to the classes used in the animation.

## Classes used in Animation
  **1.MorphFabToDialog.java**:
  A custom transition that morphs a circle into a rectangle, changing it's background color and extends changeBounds transition.
while defining custom transitons there are three methods we need to override `capture starting values,capturing end values and createAnimator`.
`super.captureStartValues(transitionValues)` && `super.captureEndValues(transitionValues)` the framework calls this method for every view in the starting scene and ending scene respectively, after this we should be adding custom values to transistionValues.
We can get reference of the view that is transistioning using  `transitionValues.view` and also it contains Map instance in which you can store the view values(as said before) you want, in this example  `rectMorph:color` and `rectMorph:cornerRadius` are the two map keys for which we enter values as accent color and 4dp respectively.
And the third method that is createAnimator return animator for animating between captureStartValues and captureEndValues. Since we animate both color and corner radius we will be returning animator set(which is derived from animator),also we ease in childview of the view being animated.

**Side note**:
Here the animatorset uses  playTogether method so that animations are performed simultanously.
For more info about the custom transistions visit the official [developer site](http://developer.android.com/training/transitions/custom-transitions.html).

**2.MorphDialogToFab.java**:
A custom transition that morphs a rectangle into a circle and functionality is exactly oposite of the above explained transition.

**3.MorphDrawable.java**:
In both the transitions we use a custom drawables called `MorphDrawable` whose functionality is to morph size, shape (via it's corner radius) and color given them as a paramters.
This class extends Drawable, basically set the corner alpha and the color in the canvas and draws them during animation.
For more about drawable check the official [developer site](http://developer.android.com/reference/android/graphics/drawable/Drawable.html).

Having described the classes,this is how we start an activity passing shared view,and transistion name along with current activity to  `makeSceneTransitionAnimation` method of ActivityOptions.

## Calling Activity
{% highlight java %}
Intent intent = new Intent(MainActivity.this, DialogActivity.class);
ActivityOptions options = ActivityOptions.makeSceneTransitionAnimation(MainActivity.this, fab, getString(R.string.fabtransition));
startActivity(intent ,options.toBundle());
{% endhighlight %}


## Receiving Activity
And in the reciving activity, in the on create method,we set the motion path along with the horizontal and vertical angle's.

{% highlight java %}
 ArcMotion arcMotion = new ArcMotion();
 arcMotion.setMinimumHorizontalAngle(50f);
 arcMotion.setMinimumVerticalAngle(50f);
{% endhighlight %}

Creating instances of `MorphFabToDialog` and `MorphDialogToFab` set motion path as arc motion.
`setSharedElementEnterTransition()` and `setSharedElementReturnTransition()` for the `getWindow()` object of the Activity.

 {% highlight java %}
 MorphFabToDialog sharedEnter = new MorphFabToDialog();
 sharedEnter.setPathMotion(arcMotion);
 sharedEnter.setInterpolator(easeInOut);
 MorphDialogToFab sharedReturn = new MorphDialogToFab();
 sharedReturn.setPathMotion(arcMotion);
 sharedReturn.setInterpolator(easeInOut);
 Window window = getWindow();
 window.setSharedElementEnterTransition(sharedEnter);
 window.setSharedElementReturnTransition(sharedReturn);
 {% endhighlight %}

And then we create interpolator for  both shared enter transistion and exit transistion.

 {% highlight java %}
  Interpolator easeInOut = AnimationUtils.loadInterpolator(this, android.R.interpolator.fast_out_slow_in);
 {% endhighlight %}


And the best part of the trick is we have added custom theme for the activity to appear as a dialog.
{% highlight xml %}
<style name="AppTheme.Dialog" parent="AppTheme.NoActionBar">
    <item name="android:windowIsTranslucent">true</item>
    <item name="android:windowBackground">@color/dialog_background_scrim</item>
 </style>

<style name="AppTheme.NoActionBar">
   <item name="windowActionBar">false</item>
   <item name="windowNoTitle">true</item>
</style>
{% endhighlight %}

 which ofcourse extends  `AppTheme.NoActionBar`   `dialog_background_scrim` color code is `#99323232`.

## More Reading:
 1.To find out more in detail check this blog [post](http://hujiaweibujidao.github.io/blog/2015/12/13/Fab-and-Dialog-Morphing-Animation/).<br/>
 2.And the `source code` is available [here](https://github.com/hujiaweibujidao/FabDialogMorph).<br/>
