---
layout: post
category : lessons
comments: true
tagline: "Hello world"
tags : [intro, beginner, swift, tutorial]
---


# What is Swift?

Swift is a new Apple open-source language used to develop iOS Apps.
It is open source and available now on [GitHub](https://github.com/apple/swift); replacing Objective-C as the main language for the iOS and OSX platforms.

The advantages of Swift over Objective-C are:

 1. Faster - Swift apps compile faster then their obj-c counterparts.  
 2. Safe   - Safe - Swift focuses a lot on ‘let’ and ‘var’ which makes the code safe by avoiding any unintended overwrite of values.
 3. Strong - Swift supports generic, enums and a lot of new features which makes it a better language to develop in.


{% highlight swift %}
let helloWorld = "Hello World"
print(helloWorld)
{% endhighlight %}

## First Project
Its time to dive right into exploring swift. Lets get started

### Open Xcode and create a new playground
We will be using the playground to learn basic swift before progressing on to make an app.
Go Ahead create a new playground from the **File->New** option.  
First screen shall look like this
![Playground](http://i.imgur.com/QOSeJQV.jpg)

Replace the contents with the following

{% highlight swift %}

// this is a let variable which is constanct and cant be changed once after initialized
let helloWorld = "Hello World"

var anotherVar = "This is another variable whose value can be changed"

//Uncommenting below line will throw a error
//helloWorld = "Changing value not allowed for let"

anotherVar = "Now i have a new value"

//ARRAYS
var myArr = ["First String", "Second String", "Third String"];
// access any value of array using the square brackets
print(myArr[0]) // 0,1,2 any can be used

//DICTIONARY
var myDict:Dictionary<String,Any> = ["Key":"AB", "SecondKey":1]
myDict["Key"]
myDict["newKey"] = "newValue"

{% endhighlight %}

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