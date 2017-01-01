---
layout: post
comments: true
title: App Shortcuts
---

An effort reducing effort in Android OS for the users.

Android 7.1 release includes many interesting updates. App Shortcuts is one of them.


<strong>What is App Shortcuts:</strong> App Shortcuts is a list which will be shown on top of the launcher icon while it is long pressed. App Shortcuts allows the user to land a page directly in the app by long pressing and selecting through launcher icon. The only option we had to launch the MAIN activity by clicking on app icon has become more luxury to hold 5 more options to decide.In simple, it is an App launcher icon’s instant menubar.

It basically reduces the effort taken by the user to visit a particular page. Also, the user can long press to drag a shortcut to pin it the home screen. Which is called pinned shortcuts and user can add and remove. The app can disable the pinned shortcuts but cannot add or remove programmatically.

Below image will be obvious to understand how it works.
<p align="center">
<img src="/assets/shortcuts/app_shortcuts_example.png" alt="Appshortcuts screenshot" style="width: 250px; display: block;" />
</p>

These shortcuts can be added statically through an XML file or dynamically by adding some piece of code inside the app.

<strong>Limitation:</strong> Maximum of 5 shortcuts can be accommodated per icon. Including both static and dynamic shortcuts.

I am going to share my experience on implementing App Shortcuts. Code for demo app is available on [Github](https://github.com/karthikraj-duraisamy/AppShortcutsDemo).


+ <strong>How to implement Manifest shortcuts which is also referred as Static Shortcuts</strong>
+ <strong>How to implement Dynamic shortcuts</strong>

# <strong>Manifest shortcuts:</strong>

Basically, the app which will not contain any action items that might be changed based on user interaction can go for it. <strong>Static shortcuts</strong> are defined in the XML file. For these shortcuts, we cannot change the title, desc and images at run-time. 

>So, only way to update them is to have a new build.

We need to add the below XML file in *'res/xml/shortcuts.xml'*

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
	<shortcut
        	android:shortcutId="new_task_shortcut"
        	android:enabled="true"
        	android:icon="@drawable/ic_new_task"
        	android:shortcutShortLabel="@string/new_task_shortcut_short_label"
        	android:shortcutLongLabel="@string/new_task_shortcut_long_label">
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="net.karthikraj.demo.manifestshortcut"
            android:targetClass="net.karthikraj.demo.manifestshortcut.NewTaskActivity"/>
    </shortcut>
</shortcuts>
{% endhighlight xml %}

The above XML file contains the new root element called *shortcuts*, 
which will hold the list of shortcuts.

In the above XML file, notable items are

+ <strong>android:shortcutId</strong> - An id to refer the shortcut.
+ <strong>android:shortcutShortLabel</strong> - Mandatory and 10 char recommended.
+ <strong>android:shortcutLongLabel</strong> - 25 char recommended. 
+ <strong>android:shortcutDisabledMessage</strong> - Message to be shown while the shortcut is disabled.


We need an entry in Manifest file. Here below the changes related to App Shortcuts In Android manifest file.

{% highlight xml %}
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
             package="net.karthikraj.appshortcutsdemo">
  <application>
    <activity android:name="">
      <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
      <meta-data android:name="android.app.shortcuts"
                 android:resource="@xml/shortcuts" />
    </activity>
  </application>
</manifest>
{% endhighlight xml %}

The *'meta-data'* mentioned in the Android manifest file needs to be placed into the Launcher activity tag. That’s it. Now we can see the App Shortcuts for the app icon. See the result below,

![Appshortcuts demo](/assets/shortcuts/manifest_shortcuts_simple.png)

In case, we want to have some Activity to be listed in the back stack and when the user clicks on the back button we may need to take them to <strong>Dashboard</strong> or <strong>Main screen</strong>, then we can have a stack of *'Activities'* in the *'shortcuts xml'* like the below code.

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
    <shortcut
        android:shortcutId="new_task_shortcut"
        android:enabled="true"
        android:icon="@drawable/ic_new_task"
        android:shortcutShortLabel="@string/new_task_shortcut_short_label"
        android:shortcutLongLabel="@string/new_task_shortcut_long_label">
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="net.karthikraj.demo.manifestshortcut"
            android:targetClass="net.karthikraj.demo.manifestshortcut.NewTaskActivity"/>
    </shortcut>
    <shortcut
        android:shortcutId="completed_tasks_shortcut"
        android:enabled="true"
        android:icon="@drawable/ic_completed"
        android:shortcutShortLabel="@string/completed_tasks_shortcut_short_label"
        android:shortcutLongLabel="@string/completed_tasks_shortcut_long_label"
        android:shortcutDisabledMessage="@string/completed_tasks_shortcut_disabled_message">
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="net.karthikraj.demo.manifestshortcut"
            android:targetClass="net.karthikraj.demo.manifestshortcut.TodoMainActivity"/>
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="net.karthikraj.demo.manifestshortcut"
            android:targetClass="net.karthikraj.demo.manifestshortcut.CompletedTasksActivity"/>
    </shortcut>
    <shortcut
        android:shortcutId="inprogress_tasks_shortcut"
        android:enabled="true"
        android:icon="@drawable/ic_in_progress"
        android:shortcutShortLabel="@string/inprogress_tasks_shortcut_short_label"
        android:shortcutLongLabel="@string/inprogress_tasks_shortcut_long_label"
        android:shortcutDisabledMessage="@string/inprogress_tasks_shortcut_disabled_message">
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="net.karthikraj.demo.manifestshortcut"
            android:targetClass="net.karthikraj.demo.manifestshortcut.TodoMainActivity"/>
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="net.karthikraj.demo.manifestshortcut"
            android:targetClass="net.karthikraj.demo.manifestshortcut.InProgressTasksActivity"/>
    </shortcut>
</shortcuts>
{% endhighlight xml %}


In the above *'xml'* code we have a list if *'intents'* added for *'shortcut'*. The last intent will be considered as the Activity to launch when the shortcut is clicked. Above intents will be added to back stack, So that user will see the back stacked activities while they press the back button. See the result below,

![Appshortcuts demo](/assets/shortcuts/manifest_shortcuts_backstack.png)


<strong>Note:</strong> While a shortcut is removed in an updated version and the same is pinned in users screen, the system will disable them automatically.

# <strong>Dynamic shortcuts:</strong>

Dynamically adding shortcuts in *'java'* code can be achieved by using *'ShortcutManager'*. Here below the sample code to achieve it,


{% highlight java %}
ShortcutManager shortcutManager = context.getSystemService(ShortcutManager.class);
Intent newTaskIntent = new Intent(context, NewTaskActivity.class);
newTaskIntent.setAction(Intent.ACTION_VIEW);
newTaskIntent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
ShortcutInfo postShortcut
        = new ShortcutInfo.Builder(context, NEW_TASK_SHORTCUT_ID)
        .setShortLabel(context.getString(R.string.new_task_shortcut_short_label))
        .setLongLabel(context.getString(R.string.new_task_shortcut_long_label))
        .setIcon(Icon.createWithResource(context, R.drawable.ic_new_task))
        .setIntent(newTaskIntent)
        .build();
shortcutManager.addDynamicShortcuts(Arrays.asList(postShortcut));
{% endhighlight java %}

That’s it. We have added dynamic shortcuts into the app. By running the app we can see the added shortcuts.


For back stack support, we can add more than one shortcut intent to the list and the last item will be considered as target Activity and remaining will be considered for Activity Back stack.

{% highlight java%}
ShortcutManager shortcutManager = context.getSystemService(ShortcutManager.class);
Intent completedTasksIntent = new Intent(context, CompletedTasksActivity.class);
completedTasksIntent.setAction(Intent.ACTION_VIEW);
completedTasksIntent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
ShortcutInfo postShortcut
        = new ShortcutInfo.Builder(context, COMPLETED_SHORTCUT_ID)
        .setShortLabel(context.getString(R.string.completed_tasks_shortcut_short_label))
        .setLongLabel(context.getString(R.string.completed_tasks_shortcut_long_label))
        .setDisabledMessage(context.getString(R.string.completed_tasks_shortcut_disabled_message))
        .setIcon(Icon.createWithResource(context, R.drawable.ic_completed))
        .setIntents(new Intent[]{
                new Intent(Intent.ACTION_MAIN, Uri.EMPTY, context, TodoMainActivity.class).setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK),
                completedTasksIntent
        })
        .build();
shortcutManager.addDynamicShortcuts(Arrays.asList(postShortcut));
{% endhighlight java%}

The result in below images,
![Appshortcuts demo](/assets/shortcuts/dynamic_shortcuts_completed.png)

> Remember, <strong>addDynamicShortcuts(List)</strong> method will always add on top of the existing shortcuts for the app and <strong>setDynamicShortcuts(List)</strong> method will always redefine the entire list.


### What if we want to update the existing shortcut?
<strong>udateShortcuts(List)</strong> method will do this trick.

### What if we want to remove the shortcuts?
As the name suggests <strong>removeAllDynamicShortcuts(List)</strong> will remove the list of shortcuts passed as a parameter.
In case the shortcut is been pinned in the Home Screen while we call <strong>removeAllDynamicShortcuts(List)</strong>, then pinned shortcuts will be disabled.

### To Disable and Enable the shortcuts?
<strong>disableShortcuts(List)</strong> will simply disable the mentioned shortcut. <strong>disableShortcuts(List, CharSequence)</strong> will disable and attach an error message to the shortcut which will be displayed as a warning message to the user while pressing it.
<strong>enableShortcuts(List)</strong> method will enable mentioned shortcuts.

### Extras:

+ When we add or update the shortcuts in the background, we have a limitation to do so. For limitation, the counts will be monitored by the system. So continuous update from the background can be denied once the limit reaches. We can always check the limit by using <strong>isRateLimitingActive()</strong>. *This limit will get reset once the app comes to the foreground.*
+ We can always get the list of pinned shortcuts by using <strong>getPinnedShortcuts()</strong>.
+ We can always get the list of manifest and dynamic shortcuts using <strong>getManifestShortcuts()</strong> and <strong>getDynamicShortcuts()</strong> respectively.







<script id="dsq-count-scr" src="//karthikraj-net.disqus.com/count.js" async></script>



{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/

var disqus_config = function () {
this.page.url = 'http://karthikraj.net/2016/11/24/android-app-shortcuts/';
this.page.identifier = '20161124SHORTCUTS'; 
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

