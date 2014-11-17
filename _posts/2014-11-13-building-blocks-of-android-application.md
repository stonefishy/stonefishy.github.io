---
layout: post
title: "Building blocks of Android Application
"
description: ""
category: Technique
tags: [Android]
---
{% include JB/setup %}
#Building blocks of Android Application
---

There are four builidng blocks for Android Application:   
   
- **`Activity`**
- **`Intent Receiver`**
- **`Service`**
- **`Content Provider`**

But Not every application needs have all them.   

`AndroidManifest.xml` is XML file, it contains some information that where you declare the components of your application and what their capabilities and requirements are. 

<!--break-->  

###Activity
Activities are the most common of the four Android building blocks. An activity is usually a single screen in your application. Each activity is implemented as single class that extends the Activity base class.    
  
When a new screen is opened, the previous screen is paused and put onto a history stack. The user can navigate backward through previously opened screens in the history.

###Intent
Android uses a special class called `Intent` to move from screen to screen. Intent describe what an application wants done. The two most important parts of the intent data structure are the action and the data to act upon.    
  
Typical values for actioin are `MAIN` (the front door of the application), VIEW, PICK, EDIT, etc. The data is expressed as a Uniform Resource Indicator (URI).    
>new Intent(android.content.Intent.VIEW_ACTION, ContentURI.create("http://anddev.org");

###Intent Filter
Navigating from screen to screen is accomplished by resolving intents. An activity calls `startActivity(myIntent)` to navigation. The system then looks at the intent filters for all installed applications and picks activity whose intent filters best matches `myIntent`. And the new activity is informed by the intent which cause it to be launched.   

###Intent Receiver
You can use an IntentReceiver when you want code in your application to execute in reaction to an external event, for example, when the phone rings, or when the data network is available, or when it's midnight. Intent receivers do not display UI.   
    
Intent receivers can be registered in the `AndroidManifest.xml` file, and also you can register it in your code with `Context.registerReceiver()`.    

Applications can also send their own intent broadcasts to others with `Context.broadcastIntent()`.   

###Service
A `Service` is long-lived and runs without a UI, such as media player.   

You can start a service with `Context.startService()` to run in the background. And you can connect to a service with the `Context.bindService()` method. When you connected to a service. you can communicate with it through an interface exposed by the service. For the music service you can pause and rewind it etc.   

###ContentProvider
Applications can store their data in files, a SQLite database, preferences or any other mechanism that makes sense.   

A content provider is useful if you want your application's data to be shared with other applications.    

A content provider is a class that implements a standard set of methods to let other applications store and retrieve the type of data that is handled by that content provider.