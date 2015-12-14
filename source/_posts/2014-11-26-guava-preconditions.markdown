---
layout: post
title: "Guava -- Preconditions"
date: 2014-11-26 22:36:07 +0800
comments: true
categories: [Guava, Java]
---

Guava provides a number of precondition checking utilities. It's `Preconditions` class. You can use it into your project.

###Usage
`checkArgument(boolean)`   
Checks that the boolean is true. Use for validating arguments to methods.   

`checkNotNull(T)`   
Checks that the value is not null. Returns the value directly, so you can use checkNotNull(value) inline. 
<!--more-->  

`checkElementIndex(int index, int size)`   
Checks that index is a valid element index into a list, string, or array with the specified size. An element index may range from 0 inclusive to size exclusive. You don't pass the list, string, or array directly; you just pass its size.
Returns index.   

`checkPositionIndex(int index, int size)`   
Checks that index is a valid position index into a list, string, or array with the specified size. A position index may range from 0 inclusive to size inclusive. You don't pass the list, string, or array directly; you just pass its size.
Returns index.   

###Example
``` java 

int i = 3;
Preconditions.checkArgument(i > 5, "Argument %s is not larger than 5", i);
// will throw IllegalArgumentException with "Argument 3 is not larger than 5"

Preconditions.checkNotNull(i); // return 3;
Preconditions.checkNotNull(null); // throw NullPointerException;

Preconditions.checkElementIndex(i,4); // return 3
Preconditions.checkElementIndex(i,3); // throw IndexOutOfBoundsException

Preconditions.checkPositionIndex(i,3); // return 3
Preconditions.checkPositionIndex(i,2); // throw IndexOutOfBoundsException

```

