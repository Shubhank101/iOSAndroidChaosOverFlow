---
layout: post
category : lessons
tagline: "Hello Android"
tags : [intro, beginner, android, tutorial]
---

> This is a general summarized version of our take on explaining Android development for you. Its by no means a complete and extensive tutorial that covers everything. This is to give you a good grasp of things of the framework, best practices used by us and generally a simple tutorial after which you are ready to take on any topic you are interested in Android :)

# What is Android?

![Android Logo](http://i.imgur.com/jK415Ks.png)

Android is a open-source operating system developed by Google released in 2008 and has gone on then to become one of the most popular OS for mobile devices.  
Apps can be developed for android in many languages including java, scala, kotlin, swift and so on.


## Android Studio

Android Studio is the official IDE released by Google to develop android apps. Basic interface of the studio is explained below

![Android Studio](http://i.imgur.com/wCvWxff.jpg)

Why you should use android studio and not other IDE like eclipse/Intelli J etc ?

* **Android Focused**  
  All the tools provided in android studio are meant to help in android projects. Huge templates support, layout editor etc are the features only provided in it.   
  
  
* **Official Support from Google**  
  Its the IDE which the google developers use and hence you can always expect it to be made better for developers each day.
  
  
  
## Android UI Components  

You define your application screen UI in xml files/graphical editor. The xml files mostly consider of using UI components provided by the framework to layout the elements on the screen. We list out the most common one used here

## Layouts
Layouts are the UI component that controls the positioning and layout of their children. You can nest layout inside one another.

### LinearLayout

Linearlayout is a commonly used layout to (as the name suggests) lay the children UI Views linearly either in a horizontal stack or a vertical.

![Linear Layout](http://i.imgur.com/B4lFtoh.jpg)


### RelativeLayout

RelativeLayout lay it children in relation to one another or to itself. You can use `alignParentLeft` to make the view position at top-left and `alignCenterInParent` to make view center in the whole layout.

![Relative Layout](http://i.imgur.com/NOoZprc.jpg)

### FrameLayout

FrameLayout works on the z axis/order basis. Other layouts do not allow you to specify views overlapping one another however frame layout does.

![Frame Layout](http://i.imgur.com/lw3vr7p.jpg)


There are more layouts waiting to be explored like DrawerLayout, GridLayout etc but they are specific and we are not explaining them here for now. The above explained layouts are the most commonly used to design any complex UI screen you wish.


## Activity

Activity are the basic individual screen component in Android. When you launch an app, you basically open one of its screen and that is an *Activity*. Its basically the **controller** part in an MVC app.

## Fragments

Fragments are the middleware between the controller and the view in a MVC model. Main difference b/w fragment and activity is that fragment needs to be hosted in a activity therefore they do not exist on their own. They always need to be added to an activity's view to be shown.

### Which one to use and when

1) Well there has to be one activity at least in your app, otherwise no UI screen can be launched.  
2) Now, do you want the view you are designing to be launched individually or do you want everytime user to go from A->B. If you want B be to be allowed to launch individually : it needs to be an activity.
3) Are you making a resuable kind of view, well not exactly a view but a wrapper that shows a particular view and act as its controller : if yes, go for a fragment.



## Manifest

Manifest is the file that describes how your app appear to the OS and in the play store. It contains certain terms that define app features, descriptions, list of activities etc.
App's manifest includes these  
 * App Package Identifier - this is a identifier that uniquely identifies your app and is in the reverse domain format (com.domain.appname)  
 * App name - Name to be shown on user device/play store  
 * Icon file - Which is the icon to show in app drawer.  
 * List of activities - Each activity/individual screen is defined in the manifest.  
 * Permissions - Your app require maybe access to location or maybe read user contacts.  
 * Features - Your app is defined for only call enabled devices (not tablets).  
 * Min OS version supported - Defines which is the OS that can install the app and none below can.  
 


# Gradle
 
 


## Conclusion

**Thank you** for reading this far. For any questions you are welcome to ask in our chat room.

## Next Steps

Please take a look at our other [tutorials]({{ BASE_PATH }}../../tutorials) :)