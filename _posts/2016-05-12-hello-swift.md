---
layout: post
category : lessons
tagline: "Hello world"
tags : [intro, beginner, swift, tutorial]
---


## Overview

### What is Swift?

Swift is new apple open source language used to develop iOS Apps.  
It is open source and available now on [github](https://github.com/apple/swift).  
It replaces objective-c as the main language for the iOS and OSX platform.

The advantage over objective c consist of :

 1. Faster - Swift apps compile faster then their obj-c counterparts.  
 2. Safe   - Swift focuses a lot on let and var which makes the code safe by avoiding any un intended overwrite of values.  
 3. Strong - Swift supports generic, enums and a whole lot of new features which makes it a better language to develop in.  


{% highlight swift %}
let helloWorld = "Hello World"
print(helloWorld)
{% endhighlight %}

## First Project

1) Open Xcode and create a new playground
We will be using the playground to learn basic swift before progressing on to make a app.
Go ahead create a new playground from the File->New option.
First screen shall look like this
![Playground](http://i.imgur.com/QOSeJQV.jpg)

Replace the contents with the following

{% highlight swift %}

// this is a let variable which is constanct and cant be changed once after initialized
let helloWorld = "Hello World"

var anotherVar = "This is another variable whose value can be changed"
{% endhighlight %}




## Conclusion

Aide helper methods and strategies aimed at making it more intuitive and easier to work with Jekyll =)

**Thank you** for reading this far.

## Next Steps

Please take a look at [{{ site.categories.api.first.title }}]({{ BASE_PATH }}{{ site.categories.api.first.url }})
or jump right into [Usage]({{ BASE_PATH }}{{ site.categories.usage.first.url }}) if you'd like.