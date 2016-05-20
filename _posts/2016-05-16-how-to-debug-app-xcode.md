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

## Conclusion

**Thank you** for reading this far. For any questions you are welcome to ask in our chat room.

## Next Steps

Please take a look at our other [tutorials]({{ BASE_PATH }}../../tutorials) :)