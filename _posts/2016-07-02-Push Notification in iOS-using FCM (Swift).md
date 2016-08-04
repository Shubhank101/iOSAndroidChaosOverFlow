---
layout: post
category : lessons
tagline: "iOS Push Notification Guide - Swift"
tags : [intro, beginner, swift, tutorial]
---

> The final project is available for download from the [Github repository](https://github.com/Shubhank101/iOSAndroidChaosOverFlow-Projects)


## What is FCM?

**FCM (Firebase Cloud Messaging)** replaces Google Cloud Messaging (GCM) as the API to send/receive push notifications from.  
Most of the popular apps in the market uses push notifications to engage the user and i am sure you are interested to implement them in your app too so follow along.


### How does FCM work

![FCM Diagram](https://firebase.google.com/docs/cloud-messaging/images/messaging-overview.png)

As shown in the figure, the FCM is the middle ware between your device and the server.  
You send the message you want to send to the devices to the Firebase server and it redirects it to the appropriate device. It handles all the middle ware part like scheduling notification time, failure and success rate, unique device tokens etc.

We can use a http/xmpp server to send the message to the FCM server but in our tutorial we use the handy tool provided by the FCM guys - **Notification console**.

___

## Theory of how Push notification work on iOS

Before progressing to code and FCM working for push notification, we should do a little review of how push notification works.

**Apple Push Notification Service (APNS)** is responsible for managing devices and sending message to them. We can send our push notification payload to the APNS server with the device token and it takes care of scheduling the message as well as success/failure rate.  
Each device is **uniquely identified by its device token**. Further, an app require user to grant the permission to send push notification to user device. If the user declines - you won't get a device token and therefore can't target the user.

___

## Step 1. Asking Permission for push notifications

Create a new Xcode Project and name it **FCM_Tutorial**.

Now note down the **bundle id** and store it in a separate place, we will need it to make the `GoogleService-Info` plist file later when we add Firebase to our app.

To get the device token - we need to ask user permission for sending alert, banner and/or sound push.

![Push Notif](http://i.imgur.com/j02xEHm.png?1)

If user allow, the device will contact the APNS server and a push notification will be passed on to the App delegate function **didRegisterForDeviceToken**.
Any error if occured is received in the function **didFailtoregister**

Update your AppDelegate's `didFinishLaunchingWithOptions` method with the following code

{% highlight swift %}

func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
     // Override point for customization after application launch.
     
     let notificationTypes: UIUserNotificationType = [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound]
     
     let notificationSettings = UIUserNotificationSettings(forTypes: notificationTypes, categories: nil)
     application.registerUserNotificationSettings(notificationSettings)
     
     return true
 }
 
We create an array defining the types of notification we need and then call `registerUserNotificationSettings` so that the OS ask for the permission from the user.

{% endhighlight %}

**You cannot test push notification on simulator so make sure to have a apple dev account and a device**

## Step 2. Creating App certificate and provisioning

#### What they are and why are they needed

You must already be knowing that to run app on devices - we need to have provisioning profile with the device udid added in it.
To send push notifications we also need to create separate push certificate and upload the **.pem** or **.p12** file on our server. Firebase require you to upload the .p12 file.



#### Simple Way - Let Xcode create it

Xcode 7 has the option to automatically create provisioning profile and certificates for you.

Go to preferences in Xcode and Add your apple developer account if not already added.
![Xcode Account](http://i.imgur.com/pMjxOuA.png)

Next up is to set the team in Xcode. This will make Xcode use the appt. apple account to create the app id and provisioning for our app.  
Here is a screenshot which shows where to set the team

![Set Team](http://i.imgur.com/TeUQS9d.jpg)

Then head over to **Capabilities** tab in your target and Enable Push Notifications
![Capabilities](http://i.imgur.com/KPRpA2d.jpg)

Xcode will now register your app id and enable the push certificates for you.

### Step 3. Adding Firebase to your project

> Prerequisite : You need to have Xcode 7.3 or higher for the FCM to work.

Head over to [Firebase console](https://console.firebase.google.com/) and click **Create New Project**. Enter FCM_Tutorial as the name project and click create project. You will be redirected to a screen like this

![Firebase Console](http://i.imgur.com/41Rq8Ij.jpg)

Click Add firebase to your iOS project and in the popup - **Enter your project bundle id**

![Bundle id popup](http://i.imgur.com/gwUb9E1.jpg)

Press Add app and now you will get the **GoogleService-Info.plist** downloaded automatically. Now keep pressing continue to finish the integration steps and get back to the project.


Drag the plist you downloaded into your project root (where .xcodeProj file is). The plist file actually includes all the relevant settings for the firebase app including the firebase database url for your app.

### Setup Pods

Open a terminal and execute **pod init** in your project directory. Open the pod file in text editor or in Xcode and add these pods to the todo target

{% highlight xml %}

pod 'Firebase'
pod 'Firebase/Database'

{% endhighlight %}

Run **pod install** in terminal and once completed open the **.xcworkspace** file to open the project.


**In progress**

## Conclusion

**Thank you** for reading this far. For any questions you are welcome to ask in our chat room.

## Next Steps

Please take a look at our other [tutorials]({{ BASE_PATH }}../../tutorials) :)