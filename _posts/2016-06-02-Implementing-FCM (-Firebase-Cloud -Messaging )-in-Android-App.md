---
layout: post
category : lessons
tagline: "Implementing Firebase in android app"
tags : [intro, beginner, swift, tutorial]
---


## What is FCM?

**FCM (Firebase Cloud Messaging)** replaces Google Cloud Messaging (GCM) as the API to send/receive push notifications from.  
Most of the popular apps in the market uses push notifications to engage the user and i am sure you are interested to implement them in your app too so follow along.


### How does FCM work

![FCM Diagram](https://firebase.google.com/docs/cloud-messaging/images/messaging-overview.png)

As shown in the figure, the FCM is the middle ware between your device and the server.  
You send the message you want to send to the devices to the Firebase server and it redirects it to the appropriate device. It handles all the middle ware part like scheduling notification time, failure and success rate, unique device tokens etc.

We can use a http/xmpp server to send the message to the FCM server but in our tutorial we use the handy tool provided by the FCM guys - **Notification console**.


### Step 1. Setting up the Android project

> Prerequisite : You need to have Studio with version **1.5** or higher for the services to work.

Create a new Android Studio Project and name it FCM_Tutorial.
Now note down the **package name**, we will need it to make the `google-services.json` file.

Head over to [Firebase console](https://console.firebase.google.com/) and click **Create New Project**. Enter FCM_Tutorial as the name project and click create project. You will be redirected to a screen like this

![Firebase Console](http://i.imgur.com/41Rq8Ij.jpg)

Click Add firebase to your Android project and in the popup - **Enter your package name**

![Package popup](http://i.imgur.com/s7EuCON.jpg)

Press Add app and now you will get the **google-services.json** downloaded automatically. Now keep pressing continue to finish the integration steps and get back to the project.


Drag the json you downloaded into your project root (where .build gradle file is of the app module). The json file actually includes all the relevant settings for the firebase app including the firebase database url for your app.

### Setup Gradle

Open your project build.gradle file and add the following dependencies 

{% highlight xml %}

 dependencies {
      classpath 'com.android.tools.build:gradle:2.1.0'
      classpath 'com.google.gms:google-services:3.0.0'
  }
{% endhighlight %}

Now open your app module build.grade and add these dependencies
{% highlight xml %}
    compile 'com.google.firebase:firebase-core:9.0.1'
    compile 'com.google.firebase:firebase-messaging:9.0.1'
{% endhighlight %}

At the end of the file we need to apply our google play servcies plugin :

`apply plugin: 'com.google.gms.google-services'`

Sync your gradle and you will have the firebase setup in your project.

### Step 2. Creating our TokenService

Each device need to be uniquely identified so that we can check success/failure and target individual user. Firebase provides **FirebaseInstanceId** class which takes care of creating unique device token for the current device.  
The token is refreshed randomly based on duration or other things so to avoid checking each time for new token, We have to use a Service class that extends **FirebaseInstanceIdService**.  
In it, the **onTokenRefresh** is called each time a new token is generated.


So it's time to create a new class in our project. Name it **TokenService.java** and have it extend **FirebaseInstanceIdService**. Here is our implementation

{% highlight java %}
    
public class TokenService extends FirebaseInstanceIdService {

    @Override
    public void onTokenRefresh() {
        // Get updated InstanceID token.
        String refreshedToken = FirebaseInstanceId.getInstance().getToken();
        Log.w("notification", refreshedToken);
        sendRegistrationToServer(refreshedToken);
    }

    private void sendRegistrationToServer(String token) {
    }
}

{% endhighlight %}

We get the token from the FirebaseInstanceId instance and just log it currently to the logcat so that we can use it later. You can send it to server in a production app to store it in your db.

### Step 3. Registering our service in manifest

For our TokenService to actually work we need to make sure it is added to our **AndroidManifest.xml** file.
Open up the manifest and inside our **application** tag add the service lines

{% highlight java %}
  <service
        android:name=".TokenService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
   </service>
{% endhighlight %}

Run the app and you should see the token in Logcat.

![Token in Logcat](http://i.imgur.com/o2oICrI.jpg)

### Step 4. Testing A Push notification through the Notification Console

This was really easy right. You can now use Notification Console to start sending push notification to your app. Go to the notification console as shown in the following pics

> **Note: For the notification to show make sure the app is in background and not in foreground**

![Notification Console](http://i.imgur.com/4Av4hyj.jpg)
![Compose Message](http://i.imgur.com/kc0UhSP.jpg)

In the second screenshot, you can set the Title and also send messages based on filter like all devices/groups/particular device, adjust time for your notification. We will use the **Single device** and use the token we got in #step 3 of our tutorial.

After filling the title and token, scroll down and click Send Message. You will see the notification show up in your device notification panel.

### Step 5. Creating our FCMMessageReceiverService


### let and var
Swift focuses majorly on let and var variables. 
variables defined using let are constant and cannot be changed once a value is initalized to them while the var can be changed later on.  
This introduces a focus on making the code secure with correct usage of constant variable from the start, therefore no bugs later on.

### Array
Array are defined using the square brackets []. Here we pass few values to initalize it.
Each value can be accessed using its **index**.

### Dictionary
Dictionary is a **key value object** and is used when the data has to be fetched key based.  
Common example is for a user, you dont want to have user name at 0 index, birthday at 1. This is problematic and confusing.  
What you want is username key to have the name value and user_birthday to have the birthday value. Something like :

{% highlight swift %}
let userDict = ["username":"Shubhank", "birthday":"unknown"]
{% endhighlight %}


Go Ahead and check the playground to see all this live. We have added comments there for you to understand along.

## Conclusion

**Thank you** for reading this far. For any questions you are welcome to ask in our chat room.

## Next Steps

Please take a look at our other [tutorials]({{ BASE_PATH }}../../tutorials) :)