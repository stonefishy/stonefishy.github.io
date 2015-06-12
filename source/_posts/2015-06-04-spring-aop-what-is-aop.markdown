---
layout: post
title: "Spring AOP - What is AOP?"
date: 2015-06-04 20:43:03 +0800
comments: true
categories: [Java, Spring, AOP]
---
What is `AOP`?, `AOP` stand for `Aspect Oriented Programming`. It's very important module for `Spring` framework. In the enterprise level application programming we used to add different types of services to our application at runtime automatically, like logging, email, transaction, authentication services. These actions we called `cross-cutting` functionalities. `AOP` do this job.    
<!-- more -->
Let's see a normal instance:

``` java
public class MyLogicClass
{
	public void logicOne() {
		//There is my logic for one.

		//Call Authentication Service 
		//Call Logging Service
		//Call Transcation Service
		//Call Email Service
	}

	public void logicTwo() {
		//There is my logic for two.

		//Call Authentication Service 
		//Call Logging Service
		//Call Transcation Service
		//Call Email Service
	}
}
```
In above codes, we have a class called MyLogicClass, in this class there are two logic methods, and both them call authentication, logging, transaction, email services. We called these service are `cross-cutting` functionalities. So there are some problems, one is these `cross-cutting` functionalities increase the class size. Second is we need changed this class and recompile, re-deploy it again when we changed the services.    

So how to overcome these problems, can we separate the services and our logic client? Yep, we can do this in Spring `AOP`, adding these `cross-cutting` functionalities at runtime don't need to write them in the logic codes. 

So if we use `AOP`, our logic class will be like this:
``` java
public class MyLogicClass
{
	public void logicOne() {
		//There is my logic for one.
		
		//Services will be injected here automatically at runtime, but in the codebase there is nothing
	}

	public void logicTwo() {
		//There is my logic for two.

		//Services will be injected here automatically at runtime, but in the codebase there is nothing
	}
}
```

In above codes, we separate the business logic and the services, we call this process of separation as `AOP`. So Using `AOP` business logic and `cross-cutting` functionalities are implemented separately and executed at runtime as combine automatically.