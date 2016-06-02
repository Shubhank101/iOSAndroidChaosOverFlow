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
        
        // first section
        let dateFormatter = NSDateFormatter()
        dateFormatter.dateFormat = "dd/MM/yyyy hh:mm a"
        
        todo?.name = self.nameField.text
        todo?.message = self.messageField.text
        todo?.reminderDate = dateFormatter.stringFromDate(self.datePicker.date)
        
        //second section
        let ref = FIRDatabase.database().reference()
        let key = ref.child("todoList").childByAutoId().key
        
        let dictionaryTodo = [ "name"    : todo!.name! ,
                               "message" : todo!.message!,
                               "date"    : todo!.reminderDate!]
        
        let childUpdates = ["/todoList/\(key)": dictionaryTodo]
        ref.updateChildValues(childUpdates, withCompletionBlock: { (error, ref) -> Void in
               self.navigationController?.popViewControllerAnimated(true)
        })
{% endhighlight %}

Our code first section does the typical model object filling by getting the values from our outlets. You can go ahead and add validation logic here if you want. The real firebase work is done in the second section.


In Second section , we get the database reference first and then try to get the "todoList" child in our db by calling `ref.child("todoList")`.
Since writing to our todoList endpoint each time will just overwrite previous values, we need to get a unique key for our new todo so that we can push it for that particular key only. We call the `childByAutoId().key` on our todoList endpoint.


Now we need to convert our model into a dictionary since Firebase cannot save custom classes. **NSString/NSArray/NSNumber and NSDictionary are the only supported types**.
On the next line we define the childUpdate model that is for /todoList/key end point => set our dictionary object.
Calling the updateChildValues performs the actual operation and we can pass a callback closure to detect any error that might have occured in the request.

Try running the app now and click the add button in first VC -> goes to TodoViewController and in it save a new Todo. Firebase console should start showing up data that we save in the app.

### Step 6. Retreiving all our Todo from the DB

Firebase allow you to constantly listen to a end point for any value but in our app we only want to load data once.
ViewController code is given below :
{% highlight swift %}

    var todoList = [Todo]()
   
    override func viewWillAppear(animated: Bool) {
        super.viewWillAppear(animated)
        loadData()
    }
    
    func loadData() {
        self.todoList.removeAll()
        let ref = FIRDatabase.database().reference()
        ref.child("todoList").observeSingleEventOfType(.Value, withBlock: { (snapshot) in
            if let todoDict = snapshot.value as? [String:AnyObject] {
                for (_,todoElement) in todoDict {
                    print(todoElement);
                    let todo = Todo()
                    todo.name = todoElement["name"] as? String
                    todo.message = todoElement["message"] as? String
                    todo.reminderDate = todoElement["date"] as? String
                    self.todoList.append(todo)
                }
            }
            self.tableView.reloadData()
            
          }) { (error) in
                print(error.localizedDescription)
          }

    }
    
    //MARK: TableView datasource
    
    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.todoList.count
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("ToDoCell")
        cell!.textLabel?.text = todoList[indexPath.row].name
        return cell!
    }

{% endhighlight %}

First we declare a array which will hold all our Todo model objects. Next we call the loadData method in our `viewWillAppear` method so that our data is refreshed everytime app is launched as well as when we come back to the view after saving a new Todo.
In **loadData** we get the reference to our **todoList** endpoint and call the **observeSingleEventOfType** method since that observes the value event only one and is not kept in memory all the time listening for any real time changes in our end point. In the closure **snapshot.value** contains the value of that endpoint.

If you observe the db in the console; each todo is uniquely identified by a key therefore in our case the snapshot.value is actually a dictionary with key value mapping. We cast it into a `[String:AnyObject]` dictionary using if let and then enumerate it. Each object contains the name,message and date of our Todo, we fill up our model with that and add it to the array. In the end we reload tableview to show the list of Todo in our UI.


### Step 7. Viewing existing Todo

We do have our todo being retrieved and the name being shown in tableview but clicking one should go to the **TodoViewController** and show all details right ? So add *didSelectRow* method in your ViewController class

{% highlight swift %}

func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
    let todoVC = self.storyboard!.instantiateViewControllerWithIdentifier("ToDoVC") as! ToDoViewController
    todoVC.todo = todoList[indexPath.row]
    self.navigationController?.pushViewController(todoVC, animated: true)
}

{% endhighlight %}
Real simple code right there. Now you need to show those details in the outlets in your TodoViewController class. Update it with the following code


{% highlight swift %}
override func viewDidLoad() {
    super.viewDidLoad()
    if self.todo != nil {
        nameField.text = self.todo?.name
        messageField.text = self.todo?.message
        
        let dateFormatter = NSDateFormatter()
        dateFormatter.dateFormat = "dd/MM/yyyy hh:mm a"
        let date = dateFormatter.dateFromString(self.todo!.reminderDate!)
        datePicker.date = date!
    }
}
{% endhighlight %}

That's it to have our todo app functioning on our firebase database :).

## Conclusion

**Thank you** for reading this far. For any questions you are welcome to ask in our chat room.

## Next Steps

Please take a look at our other [tutorials]({{ BASE_PATH }}../../tutorials) :)