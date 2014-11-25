---
layout: post
title: "Guava -- Optional"
description: ""
category: Java, Guava
---

`Optional<T>` is a way of replacing a nullable T reference with a non-null value. An Optional may either contain a non-null T reference (in which case we say the reference is "present"), or it may contain nothing (in which case we say the reference is "absent"). It is never said to "contain null."

<!--more-->
###Usage

`Optional.of(T)`      
Make an Optional containing the given non-null value, or fail fast on null.    

`Optional.absent()`   
Return an absent Optional of some type.

`Optional.fromNullable(T)`   
Turn the given possibly-null reference into an Optional, treating non-null as present and null as absent.   

`boolean isPresent()`      
Returns true if this Optional contains a non-null instance.

`T get()`   
Returns the contained T instance, which must be present; otherwise, throws an IllegalStateException.

`T or()`   
Returns the present value in this Optional, or if there is none, returns the specified default.

`T orNull()`   
Returns the present value in this Optional, or if there is none, returns null. The inverse operation of fromNullable.


###Example:
``` java   
Optional<Integer> numberOptional = Optional.of(100);
numberOptional.isPresent(); //return true
numberOptional.get(); //return 100

numberOptional = Optional.absent();
numberOptional.isPresent(); // return false
numberOptional.or(0); //return 0;
numberOptional.orNull(); //return null;

numberOptional = Optional.fromNullable(1);
numberOptional.get(); //return 1;

numberOptional = Optional.fromNullable(null);
numberOptional.isPresent(); //return false;
numberOptional.orNull(); //return null;

```  

