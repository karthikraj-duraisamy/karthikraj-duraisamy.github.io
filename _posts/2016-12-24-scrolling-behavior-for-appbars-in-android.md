---
layout: post
comments: true
title: Scrolling Behavior for Appbars in Android
---

App bars contains four main aspects, that plays huge role in scrolling behavior. They are,

+ Status Bar
+ Toolbar
+ Tab bar/Search bar
+ Flexible space

<p align="center">
<img src="/assets/scrolling/appBarOverview.png" alt="AppBar Overview" style="width: 250px; display: block;" />
</p>


AppBar scrolling behavior enriches the way contents in a page presented.

I am going to share my experience about how easily we can understand and use the elevation in scrolling, sizing the flexible spaces, how to anchor specific elements.

App Bar has following scrolling options,


1. <strong>Standard App bar scrolling with only Toolbar</strong>
2. <strong>App bar scrolling with tabs</strong>
3. <strong>App bar scrolling with Flexible space</strong>
4. <strong>App bar scrolling with image in Flexible space</strong>
5. <strong>App bar scrolling with overlapping content in Flexible space</strong>

If you wish to jump into the code directly, Here is the [Github](https://github.com/karthikraj-duraisamy/ScrollingBehaviorAndroid) repository link.


<strong>Basic Setup</strong>

Before we start jumping in and see all types of scrolling behavior, we needs to be clear about the basic setup and implementation.

Use design support library to achieve AppBar scrolling behavior. This library provides many of the material design components.

In app build.gradle,

{% highlight gradle %}
dependencies {
    compile 'com.android.support:design:X.X.X'
}
{% endhighlight gradle %}


Extend <strong>android.support.v7.app.AppCompatActivity</strong> in the <strong>Activity</strong> class.
{% highlight java %}
public class MainActivity extends AppCompatActivity {
{% endhighlight java %}


In the layout xml, we need to have <strong>CoordinatorLayout</strong> in the top. Add <strong>Toolbar</strong> inside <strong>AppBarLayout</strong> and the <strong>AppBarLayout</strong> needs to be inside the <strong>CoordinatorLayout</strong>. <strong>CoordinatorLayout</strong> is the one, which gives proper scrolling and material animations to the views attached with it like <strong>FloatingButtons, ModalSheets</strong> and <strong>SnackBar</strong>.


{% highlight xml %}
<android.support.design.widget.CoordinatorLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
<android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?attr/colorPrimary">
<android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"/>
</android.support.design.widget.AppBarLayout>
...
</android.support.design.widget.CoordinatorLayout>
{% endhighlight xml %}


That’s it. We have done with the basic implementation and after this, there are some flags that will decide the scrolling behavior.


# <strong>Standard App bar scrolling with only Toolbar</strong>

* <strong>scroll off-screen</strong> with the content and returns when the user reverse scrolls.
* <strong>stay fixed at the top</strong> with content scrolling under it.


![AppBar Scrolls along the content](/assets/scrolling/appbarscrolls.gif)



To achieve this, apart from the above basic setup code implementation:

The <strong>Toolbar</strong> needs to have <strong>app:layout_scrollFlags</strong>

{% highlight xml %}
<android.support.v7.widget.Toolbar
    ...
    app:layout_scrollFlags="scroll|enterAlways|snap"/>
{% endhighlight xml %}

<strong>scroll</strong> -will be scrolled along with the content. 

<strong>enterAlways</strong> -when content is pulled down, immediately app bar will appear.

<strong>snap</strong> -when the <strong>AppBar</strong> is half scrolled and content scrolling stopped, this will allow the <strong>AppBar</strong> to settle either hidden or appear based on the scrolled size of Toolbar.

Once <strong>app:layout_scrollFlags</strong> added to <strong>Toolbar</strong>, the content view (Either a <strong>NestedScrollView</strong> or <strong>RecyclerView</strong>) needs to have <strong>app:layout_behavior</strong> tag.

{% highlight xml %}
<android.support.v4.widget.NestedScrollView 
    ...
    app:layout_behavior="@string/appbar_scrolling_view_behavior"/>
{% endhighlight xml %}


That’s it these two tags along with basic setup is enough to achieve the <strong>Standard AppBar</strong> with <strong>Toolbar</strong> scrolling behavior. We can get different behaviors by playing with <strong>app:layout_scrollFlags</strong>.

Here is clear explanation from Android docs for the flags,


![From Android docs](/assets/scrolling/scrollFlagsExplanation.jpg)


# <strong>App bar scrolling with tabs</strong>

+ <strong>TabBar</strong> stays <strong>anchored at the top</strong>, while the <strong>Toolbar</strong> scrolls off.
+ Whole <strong>AppBar</strong> stays <strong>anchored at the top</strong>, with the content scrolling underneath.
+ Both the <strong>toolbar</strong> and tab bar scroll off with content. The <strong>TabBar</strong> returns on reverse-scroll, and the <strong>Toolbar</strong> returns on complete reverse scroll.


![demo](/assets/scrolling/appBarToolBar.gif)
![demo](/assets/scrolling/appBarToolBarScrollsFully.gif)
![demo](/assets/scrolling/appBarToolBarStaysFully.gif)


To achieve this, we need to add <strong>TabLayout</strong> inside the <strong>AppBarLayout</strong> and provide the <strong>layout_scrollFlags</strong> inside <strong>TabLayout</strong>. That will be enough to achieve this and we can play around with the scrolling behavior like above examples by just altering the <strong>layout_scrollFlags</strong>.


{% highlight xml %}
<android.support.design.widget.AppBarLayout 
    ...>
    <android.support.v7.widget.Toolbar
        .../>
    <android.support.design.widget.TabLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_scrollFlags="scroll|enterAlways|snap"/>
</android.support.design.widget.AppBarLayout>
{% endhighlight xml %}



# <strong>App bar scrolling with Flexible space</strong>


+ The <strong>flexible space shrinks</strong> until only the toolbar remains. The <strong>title shrinks to 20sp</strong> in the navigation bar. When scrolling to the top of the page, <strong>the flexible space and the title grow into place again.</strong>

+ The whole <strong>app bar scrolls off</strong>. When the user reverse scrolls, the toolbar returns <strong>anchored to the top</strong>. When scrolling all the way back, <strong>the flexible space and the title grow into place again</strong>.

![demo](/assets/scrolling/FlexibleSpaceScrolling.gif)


To get Flexible space for <strong>AppBar</strong>, we need to use <strong>CollapsingToolbarLayout</strong> around the <strong>ToolBar</strong> tag. Which means <strong>CoordinatorLayout</strong> in the top and <strong>AppBarLayout, CollapsingToolbarLayout, ToolbarLayout</strong> inside the order.

We need to add height for the <strong>AppBarLayout</strong> and need to specify <strong>app:layout_scrollFlags</strong> for <strong>CollapsingToolbarLayout</strong>. 
Also we need to add <strong>app:layout_collapseMode=”pin”</strong> tag in <strong>Toolbar</strong>.


{% highlight xml %}
<android.support.design.widget.AppBarLayout 
    android:layout_width="match_parent"
    android:layout_height="200dp">

    <android.support.design.widget.CollapsingToolbarLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:contentScrim="?attr/colorPrimary"
        app:layout_scrollFlags="scroll|exitUntilCollapsed">

        <android.support.v7.widget.Toolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_collapseMode="pin"/>

    </android.support.design.widget.CollapsingToolbarLayout>

</android.support.design.widget.AppBarLayout>

{% endhighlight xml %}


<strong>exitUntilCollapsed</strong> -flag will make the Flexible space scrolled down while scrolling back to position along with the content.


# <strong>App bar scrolling with image in Flexible space</strong>

+ Similar to the above Flexible space behavior. When scrolling image will <strong>pushed up with slight animation</strong> and the color changes to primary color.

+ While reversing the scrolling <strong>primary color fades away to leave way for the image</strong> been pulled down with a slight animation.


![demo](/assets/scrolling/imageParallaxScrolling.gif)


It is very much similar to the Flexible Space implementation with the below changes,

+ <strong>ImageView</strong> needs to added inside <strong>CollapsingToolbarlayout</strong>.

+ <strong>AppBarLayout height specified 200dp will be applied to image.

{% highlight xml %}
<android.support.design.widget.AppBarLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary">
    <android.support.design.widget.CollapsingToolbarLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:contentScrim="?attr/colorPrimary"
        app:layout_scrollFlags="scroll|exitUntilCollapsed"
        app:expandedTitleTextAppearance="@style/TextAppearance.AppCompat.Title">

        <ImageView
            android:layout_width="match_parent"
            android:layout_height="200dp"
            app:layout_collapseMode="parallax"/>
    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        app:layout_collapseMode="pin"
        android:layout_height="?attr/actionBarSize"
        app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
        app:theme="@style/ToolBarStyle" />
</android.support.design.widget.CollapsingToolbarLayout>
</android.support.design.widget.AppBarLayout>
{% endhighlight xml %}


# <strong>App bar scrolling with overlapping content in Flexible space</strong>


+ In this scrolling, the <strong>AppBar</strong> with Flexible space will be placed <strong>behind the content</strong>. Once content starts scrolling, the app bar will <strong>scroll faster than the content</strong> until it gets out of the overlapping content view. Once the content reaches top, <strong>app bar comes upside of the content</strong> and content goes underneath and scrolls smoothly.

+ The whole <strong>AppBar</strong> can <strong>scroll off-screen</strong> along with content and can be returned while reverse scrolling.

+ There will not be any <strong>TabBar</strong> placement in this behavior.


![demo](/assets/scrolling/flexiSpaceContentOverlapping.gif)


This can be achieved by using <strong>app:behaviour_overlapTop</strong> in the <strong>NestedScrollView or RecyclerView</strong>. Also in this case we are specifying height value for <strong>CollapsingToolbarLayout</strong>.

{% highlight xml %}
<android.support.design.widget.AppBarLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary">
    <android.support.design.widget.CollapsingToolbarLayout
        android:layout_width="match_parent"
        android:layout_height="172dp"
        app:contentScrim="?attr/colorPrimary"
        app:titleEnabled="false"
        app:layout_scrollFlags="scroll|exitUntilCollapsed"
        app:expandedTitleTextAppearance="@style/TextAppearance.AppCompat.Title">

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        app:layout_collapseMode="pin"
        android:layout_height="?attr/actionBarSize"
        app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
        app:theme="@style/ToolBarStyle" />
</android.support.design.widget.CollapsingToolbarLayout>
</android.support.design.widget.AppBarLayout>

<android.support.v4.widget.NestedScrollView
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    app:behavior_overlapTop="100dp"
    app:layout_behavior="@string/appbar_scrolling_view_behavior">

    ...
</android.support.v4.widget.NestedScrollView>
{% endhighlight xml %}

Also we can implement and specify the <strong>scrollFlags</strong> dynamically through java code. 


Hopefully this article will help you to implement scrolling behaviors for AppBar.


Code for demo app is available on [Github](https://github.com/karthikraj-duraisamy/ScrollingBehaviorAndroid).


If you like the article, follow me on [Medium](https://medium.com/@twit2karthikraj) and [Twitter](https://twitter.com/MeKarthikraj). You can also find me on [LinkedIn](https://in.linkedin.com/in/karthikrajduraisamy).




<script id="dsq-count-scr" src="//karthikraj-net.disqus.com/count.js" async></script>



{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/

var disqus_config = function () {
this.page.url = 'http://karthikraj.net/2016/12/24/scrolling-behavior-for-appbars-in-android/';
this.page.identifier = '20161224SCROLLING';
};

(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = '//karthikraj-net.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>

{% endif %}


