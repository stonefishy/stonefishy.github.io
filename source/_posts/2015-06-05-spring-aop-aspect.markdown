---
layout: post
title: "Spring AOP - Aspect"
date: 2015-06-05 09:25:53 +0800
comments: true
categories: [Java, Spring, AOP]
---
An `Aspect` represent the **name** of a `cross-cutting` functionality, it's only name not implementation.

Let us see the example.

``` java
public class MyLogicClass
{
	public void logicOne() {
		//This is my logic for one.

		//Call Authentication Service 
		//Call Logging Service
		//Call Transcation Service
		//Call Email Service
	}

	public void logicTwo() {
		//This is my logic for two.

		//Call Authentication Service 
		//Call Logging Service
		//Call Transcation Service
		//Call Email Service
	}
}
```
<!-- more -->
From above codes, we have four `cross-cutting` functionalities for *logicOne* and *logicTwo* method, So this means we have four `Aspect`s which are *Authentication*, *Logging*, *Transaction*, *Email*.  

*Note: `Aspect` is only denote the name of the `cross-cutting` functionality, not the implementation*