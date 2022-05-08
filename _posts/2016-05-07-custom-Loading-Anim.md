---
title:  "Custom Loading Animation"
author: karthik
date:   2016-05-07 00:18:23 +0700
categories: [Android]
tags: [Android]
---
Improve user experience by adding your own custom loading animation, this blog post talks about creating a simple loading animation.

##Introduction
Progress bars let the user know that the device is busy, and in particular the indeterminate mode the progress bar shows a cyclic or a horizontal animation without an indication of progress.
we can always improve this experience by adding our own custom loading screen, to see how this works lets take example of this sample application(Let's call it taxi sure, which helps you rent taxi) that has a simple splash screen activity.
first let's see how the application splash screens looks with the oridnary dull indeterminate progress(circular).

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/customloadingview.gif)


Now let's try some custom indeterminate progress view, let's assume app is about  `renting cars`.
Hence i will use car icons for loading icons.

![ScreenShot](https://raw.githubusercontent.com/NULLPointerGuy/NULLPointerGuy.github.io/master/static/img/_posts/carloading.gif)


##Implementation part:
Create a class called  `CarLoadingView` which extends Linear Layout, now define two constructors one is to create view through xml and the latter one is programmatically in java.

{% highlight java %}
  public CarLoadingView(Context context, AttributeSet attrs)
{% endhighlight %}

and

{% highlight java %}
  public CarLoadingView(Context context)
{% endhighlight %}


now let's consider the first constructor, which gets called during xml inflation.<br/>
please note that first,second,third and fourth are all global imageview variables.
{% highlight java %}
  LayoutInflater inflater = LayoutInflater.from(context);
  inflater.inflate(R.layout.car_loading_row,this);
  first = (ImageView) findViewById(R.id.img_1);
  second = (ImageView)findViewById(R.id.img_2);
  third = (ImageView)findViewById(R.id.img_3);
  fourth = (ImageView)findViewById(R.id.img_4);

  handler = new Handler();
  positionswitcher.run();
{% endhighlight %}

One thing to note is that the constructor inflates a xml layout called  `car_loading_row`.
Now this xml consists of a Linear Layout with orientation set horizontally and 4 Imageviews as childviews.

{% highlight xml %}
  <LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center">

    <ImageView
        android:id="@+id/img_1"
        android:layout_width="54dp"
        android:layout_height="54dp"
        android:layout_margin="8dp"
        />

    <ImageView
        android:id="@+id/img_2"
        android:layout_width="54dp"
        android:layout_height="54dp"
        android:layout_margin="8dp"
        />
    <ImageView
        android:id="@+id/img_3"
        android:layout_width="54dp"
        android:layout_height="54dp"
        android:layout_margin="8dp"
        />
    <ImageView
        android:id="@+id/img_4"
        android:layout_width="54dp"
        android:layout_height="54dp"
        android:layout_margin="8dp"
        />
  </LinearLayout>
{% endhighlight %}

we also instantiate a handler and define a runnable as shown in the constructor,  `at first we manually call run() method` of the runnable positionswitcher, later on we use handler to do the job for every 250ms.
By default all the imageviews are set with darker background images.
If you haven't noticed there are 2 images with color code #cbcbcb and #fff, were #fff is highlighted image.
The logic here is we increment a int variable called position in the runnable and call a method called switch position were we set image view with the corresponding images.
We do modulo of the position passed so that we can limit it  `upto 3(0-3) positions` only.

{% highlight java %}
  Runnable positionswitcher = new Runnable() {
        @Override
        public void run() {
            switchPosition(position);
            position++;
            //re run the same thread after the duration of 250 ms
            handler.postDelayed(positionswitcher,250);
        }
    };

  private void switchPosition(int position){
    int highlightedPosition = position % 4;
    first.setImageResource(R.mipmap.dark_city_car);
    second.setImageResource(R.mipmap.dark_sedan);
    third.setImageResource(R.mipmap.dark_suv);
    fourth.setImageResource(R.mipmap.dark_sportcar);

    switch (highlightedPosition){
     case 0:
        first.setImageResource(R.mipmap.city_car);
        break;
    case 1:
        second.setImageResource(R.mipmap.sedan);
        break;
    case 2:
        third.setImageResource(R.mipmap.suv);
        break;
    case 3:
        fourth.setImageResource(R.mipmap.sportcar);
        break;
     }
  }

{% endhighlight %}

Please note that inside runnable i use the handler object that was instantiated earlier in the constructor.
and last but not the least don't forget to add custom view in the activity layout file.

 {% highlight xml %}
  <com.karthik.customloadingapp.CarLoadingView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_marginBottom="32dp"
        android:layout_centerHorizontal="true"/>
 {% endhighlight %}

## More Reading:
 you can find the source code [here.](https://github.com/NULLPointerGuy/CustomLoadingView)
