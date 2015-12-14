---
layout: post
title: "Guava -- Common Object Methods"
date: 2014-11-27 20:41:45 +0800
comments: true
categories: [Guava, Java]	
---

###Usage
`Objects.equal(Object, Object)`   
Invoking this method then it will avoid the risking of NullPointerException when we need to compare two object whether they equal.
``` java
Objects.equal("a", "a"); // return true;
Objects.equal(null, "a"); // return false;
Objects.equal(null, null); // return true;
```
<!--more-->
`Objects.hashCode(Object, ....)`   
Generating hash code with multiple object will be simple. And it also can hash all fields of an Object to get more sensible code value.   
``` java
// generate hash code with multi object
Objects.hashCode(this.intField, this.charField, this.stringField, this.dateField); 
```

`ComparisonChain`   
ComparisonChain is a fluent idiom and much more readable. less prone to accidental typos, and smart enough not to do more work than it must.   
It only performs comparisons until it finds a non-zero result, after which it ignores further input.
``` java
ObjectsTraining objectsTraining = new ObjectsTraining();
int result = ComparisonChain.start()
			   .compare(this.intField, objectsTraining.intField)
             .compare(this.stringField, objectsTraining.stringField)
             .compare(this.charField, objectsTraining.charField)
             .compare(this.dateField, objectsTraining.dateField)
             .result();
```

