---
layout: post
category : lessons
tagline: "Debugging app in Xcode"
tags : [debug, Xcode, swift, tutorial]
---


## What is Debugging?
Debugging is the process of finding bugs in your apps and removing them. A bug can be considered anythin from a simple semicolon error to a whole logic mistake


### Why do you need to learn debugging
It is a known fact that every developer spends more time figuring out things why aren't working in the code rather than developing.
If you learn the art of debugging, you will be able to considerably reduce your time spent on fixing things and everyone will be happy.


## Lets Start

Go Ahead and download the basic project i have setup for you which we will follow along from the [github](https://github.com/Shubhank101/iOSAndroidChaosOverFlow-Projects) repository.  
Its developed in Xcode 7.3 and swift 2, so make sure to have them ready.

The folder contains both the app to be debugged and the working example for you to go through. 
Its a really simple 2 View Controllers code. The first calls the itunes search api to find list of games with keyword fifa and the next view controller just loads the game image.

___

# Time to debug

### Breakpoints
![Breakpoints in Xcode](http://i.imgur.com/kGd0APL.jpg)

I have shown the main interface provided by Xcode for debugging your app. It is divided into three parts; Debug panel, Variables panel and the Console.

### Debug Panel  
Breakpoint are used to break/pause your code at a certain point of condition (can be a line or a complex expression). They are extremely useful as you can pause your code and inspect the values to find any mistakes in your logic/program.

1. **Toggle Breakpoints** This is a easy switch to disable all breakpoints and enable them.  
2. **Play/Pause** This switch lets you pause your code at a certain time and once you are ready to continue, just press it again to continue execution.  
3. **Step Over** Step over is the most important and most used tool in debugging for me. What it does is process the current line of code and then stop the thread at the next line, so essentially you use it to.

### Variables Inspector
Variables Inspector as the name suggest allow you to check and inspect values.
The variables are categorized by scope (auto and local).
**Primitive** data types show the value right next to them while the objects inner variables can be checked by using the dropdown right next to them.


### Console
Console shows all the logs you have added in your code using `print()` function. Moreover you can use **po \<varName\>** in console to output any variable which is currently in scope (A really handy thing).

___

## Running the Project

Open the project and press **run**.  
![First Error](http://i.imgur.com/x5jrAlv.png)
Well, there you go. Your first error in action.

So how to proceed ?
Well the error points to a mutating member on a immutable object. If you already know the error and its fix, just go ahead and fix it yourself. However, in case if you dont know how to fix, its time for you to Google!.

# Google
This is the **most used** debugging technique used by programmers i.e Googling the error for a possible solution.  
From a novice to a experienced programmer, everyone has to take help of google to find the cause of the error.  
Go ahead and copy paste `cannot use mutating member on immutable value; is a let constant` into google search.    
You would have seen that i removed `appList` from my search. Since google would try to search for the string and the possibility exist that the other sources might not have the same variable name, it is advised to remove any string from your search which might be dynamic (different to each user).

### Reading Result

First few results include a link to this [Stackoverflow thread](http://stackoverflow.com/questions/32232007/immutable-value-of-array-only-has-mutating-members-named-append).
From the answers, you will find that

> When you use let you cannot change or append or add new things to this variable. change **let** to **var**

Thats it. change the declaration of `appList` variable from let to var and your project should run this time.

___

## Stack Traces
After running, you should be seeing many rows with fifa in their title. Click any of them and you will be treated with another error.

![Next Error](http://i.imgur.com/gyBXWaG.jpg)

First thing to do would be searching for the **SIGBART** error but you will soon realise that the error is vague and you wont really be able to solve this with this less info.  
You switch to stack traces this time. Stack trace is the stack of method calls to reach current function call. It helps to really see how the program logic has been executed by checking the function calls.  
Luckily, in this crash. you already got the exception and the stack trace in the console.


{% highlight swift %}
NSUnknownKeyException, reason: [<Debug_App.ShowImageViewController 0x7ffb73c515d0> setValue:forUndefinedKey:]:
this class is not key value coding-compliant for the key randomLabelOutlet.
*** First throw call stack:
(
	0   CoreFoundation                      0x000000010768fe65 __exceptionPreprocess + 165
	1   libobjc.A.dylib                     0x00000001093d2deb objc_exception_throw + 48
	2   CoreFoundation                      0x000000010768faa9 -[NSException raise] + 9
	3   Foundation                          0x0000000107a599bb -[NSObject(NSKeyValueCoding) setValue:forKey:] + 288
	4   UIKit                               0x000000010803d320 -[UIViewController setValue:forKey:] + 88
	5   UIKit                               0x000000010826bf41 -[UIRuntimeOutletConnection connect] + 109
	6   CoreFoundation                      0x00000001075d04a0 -[NSArray makeObjectsPerformSelector:] + 224
	7   UIKit                               0x000000010826a924 -[UINib instantiateWithOwner:options:] + 1864
	8   UIKit                               0x0000000108043eea -[UIViewController _loadViewFromNibNamed:bundle:] + 381
	9   UIKit                               0x0000000108044816 -[UIViewController loadView] + 178
	10  UIKit                               0x0000000108044b74 -[UIViewController loadViewIfRequired] +
  
{% endhighlight %}

If you will read from bottom to top. you will see that `loadView` method is called and then during `UIRuntimeOutletConnection` class method a crash occurs.
This is why stack trace is helpful as it gives you hint to proceed further by telling you the method calls that took place.
The exception points to **class is not key value coding-compliant for the key randomLabelOutlet**

With this much info, we are ready to solve this one. If you will google for the exception error, you will come across many threads that also points to some outlet connection being broken.
To solve this, we are going to search for `randomLabelOutlet` in our project.
And there it is.  
![Search Result](http://i.imgur.com/JAqA8kC.jpg)  

![Broken Outlet](http://i.imgur.com/fjRsCFg.png)

**Remove the broken outlet** and now clicking the first row in tableview would work in the app.

___

# Logical Errors

These are the creepy ones and we have **one** in our project :D  
Run the project and try clicking any of the bottom rows in the list. This time you will be faced with this
![image url nil](http://i.imgur.com/Sgipdlt.jpg)

A simple google search will show you need to use **if let** for optionals unwrapping but the point here is that why is our **imageURL** nil in the first place. It definitely works for the first result of the table.

### App Flow
Here is a basic recap of what the app does. The first VC downloads the app results for the tag **fifa** and in the second VC we just download the image from the app icon url and set it in a imageView.

### How to proceed

1) Set breakpoint on the `print(imageURL)` line
![Breakpoint added](http://i.imgur.com/iEkf9BL.png)

Run the code and try any of the last rows in the table or the first few rows.  
For the first few rows, the value in values inspector shows like

![First Image result](http://i.imgur.com/LaGsMzf.jpg)
___

For the last few rows :
![Second Image result](http://i.imgur.com/NHYCYZ9.jpg)
___

2) Checking the source  
As you can see image url is for some weird reason **nil** for the last rows.
To solve it, we need to think back in our code and check where is the image url set from.
Our code sets it in the `prepareForSegue` method in firstVC
{% highlight swift %}
       if let destinationVC = segue.destinationViewController as? ShowImageViewController {
            let indexPath = self.tableView.indexPathForSelectedRow
            destinationVC.imageURL = appList[indexPath!.row].url
        }
{% endhighlight %}

Seems nothing wrong here, it just get the data from the appList variable and set the url. This forces us to check if our **appList** variable actually contains proper data.
Search for the appList variable and you will soon come to source

{% highlight swift %}
  for (index,gameDict) in arr.enumerate() {
      let model = AppModel()
      model.name = gameDict["trackCensoredName"] as! String

      if (index < 10) {
          model.url = gameDict["artworkUrl512"] as? String                            
      }
      self.appList.append(model)

  }
{% endhighlight %}

There it is.. you see **if(index <10)** .. that is the culprit and removing that if condition will make this project work as it was intended to.


## Conclusion

**Thank you** for reading this far. For any questions you are welcome to ask in our chat room.

### For further info

[Apple Debugging Support](https://developer.apple.com/support/debugging/)


## Next Steps

Please take a look at our other [tutorials]({{ BASE_PATH }}../../tutorials) :)
