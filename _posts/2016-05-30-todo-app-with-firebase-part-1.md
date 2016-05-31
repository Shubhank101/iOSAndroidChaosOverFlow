---
layout: post
category : lessons
tagline: "Hello Firebase"
tags : [firebase, swift, ios, tutorial, todo app]
---

> This tutorial assumes you are familiar with basics of iOS development including Xcode, swift and cocoapods.

# What is Firebase?

![Firebase logo](https://1.bp.blogspot.com/-YIfQT6q8ZM4/Vzyq5z1B8HI/AAAAAAAAAAc/UmWSSMLKtKgtH7CACElUp12zXkrPK5UoACLcB/s1600/image00.png)

**Firebase** is a multi purpose sdk which can be used to integrate push notifications in your app, app analytics and a whole lot of other features which you can review on its site.

We are going to use **Firebase Realtime Database** here to develop a simple To-do app.

## Step 1. Setting up Xcode Project

Create a new swift Xcode Project and name it TodoApp.
Now note down the **bundle id**, we will need it to make the `GoogleService-Info` plist file.

Head over to [Firebase console](https://console.firebase.google.com/) and click **Create New Project**. Enter TodoApp as the name project and click create project. You will be redirected to a screen like this

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

## Step 2. Making the App UI

We need to setup a basic UI to actually see our firebase code in work. I am going to explain in short here with enough details to actually carry out yourself.

 Embed the initial view controller in a navigation controller (this gives us the navigation stack). Now add another view controller and set its storyboard id to **ToDoVC**.
 The UI you need to make is shown in the screenshot
 
 ![App's UI](http://i.imgur.com/eN1Y4qT.jpg)
 
 The first VC has a TableView and a UIBarButtonItem in it. TableView has a dynamic prototype cell and i have given cellIdentifier as **ToDoCell**. Make sure to make the tableview outlet and set its delegate and data source to your VC class.
 
 The bar button just segues to the TodoViewController (our second VC in the screen).
 
 The TodoViewController has three labels and two textfields + a date picker in it. Pretty simple stuff.
 Make a new **TodoViewController.swift** class in your project and assign it to the VC. Then make all the textfield and date picker outlets.
 Also add a Done/Save barbutton item in this class on which we will actually save our Todo to the firebase db. Make the appt **IBAction** for it.
 
## Step 3. Making the Model

Our Todo won't be a Array or Dictionary lying around here and there in a project. We will make a proper model class for it.
Make a new **Todo.swift** in your project and replace its content with the following

{% highlight swift %}
import UIKit

class Todo: NSObject {
    var name :String?
    var message: String?
    var reminderDate: String?
    // id which is set from firebase to uniquely identify it
    var uniqueId:String?
}


{% endhighlight %}

## Step 4. Back to understanding how Firebase works

The database your app has on the firebase cloud is available on a public url like https://appname-randomIdentifer.firebaseio.com/.
Since it is public anyone can read/write to it if the url is known. To solve this the **database rules** section is provided where you can change the access/write rights.

By default firebase database require user to be authenticated before writing to the database.

### Changing Database Rules

In this begginer tutorial we are going to remove the authentication required to read/write from our database. Note: **You should not have this for a production app**.

Go to the database section in the firebase console and change to the rules tab. Update the rules with the below and click publish.

{% highlight json %}
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
{% endhighlight %}
![Rules](http://i.imgur.com/W4a6Xjm.jpg)

## Database Structure

Firebase database is actually a NoSQL
one, you won't see any tables and where clauses in it and it is therefore **a big change** for many developers out there.

Our app has a simple Todo structure which arranged in a typical SQL database would look like.


Name     | Message    | Date
First    | Message    | 10/10/16 10:10

But in **Firebase JSON syntax**, we would have to do something like this
![Db structure](http://i.imgur.com/rGOklwM.jpg)

Each todo is uniquely identified by its key which firebase creates and our todoList is actually and array for the key **todo** in our db.


___

## Coding Actual Firebase Logic

### Step 5. Saving a new Todo in the Firebase Db

#### Intializing Firebase in our app

Open up Appdelegate.swift `didFinishLaunching` method and in it add the line
```
        FIRApp.configure()
```
Make sure to **import Firebase** in each class you use Firebase.


Now switch to TodoViewController.swift and add a optional declaration of our todo object.
{% highlight swift %}
    var todo:Todo?
{% endhighlight %}

In the done IBAction add the following line of code

{% highlight swift %}
     if todo == nil {
            todo = Todo()
        }
        
        let dateFormatter = NSDateFormatter()
        dateFormatter.dateFormat = "dd/MM/yyyy hh:mm a"
        
        todo?.name = self.nameField.text
        todo?.message = self.messageField.text
        todo?.reminderDate = dateFormatter.stringFromDate(self.datePicker.date)
        
        let ref = FIRDatabase.database().reference()
        let key = ref.child("todo").childByAutoId().key
        
        let dictionaryTodo = [ "name"    : todo!.name! ,
                               "message" : todo!.message!,
                               "date"    : todo!.reminderDate!]
        
        let childUpdates = ["/todo/\(key)": dictionaryTodo]
        ref.updateChildValues(childUpdates, withCompletionBlock: { (error, ref) -> Void in
               self.navigationController?.popViewControllerAnimated(true)
        })
{% endhighlight %}


## Conclusion

**Thank you** for reading this far. For any questions you are welcome to ask in our chat room.

## Next Steps

Please take a look at our other [tutorials]({{ BASE_PATH }}../../tutorials) :)