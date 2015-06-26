---
layout: post
title: "Spring AOP - @AspectJ Annotation"
date: 2015-06-16 17:46:08 +0800
comments: true
categories: [Java, Spring, AOP]
---

In this article, we introduce how to use `AOP` with the `@AspectJ` Annotation. If we want to use this Annotation. we should include below code in the XML schema-based configuration file.

``` xml
 <aop:aspectj-autoproxy/> 
``` 

And also you need to add these libraries to your project, *aspectjrt.jar*, *aspectjweaver.jar*, *aspectj.jar* and *aopalliance.jar*.

<!-- more -->
#### Declare Aspect
Using `@Aspect` annotation to the class which you want to appoint it as an `Aspect`.

``` java
@Aspect
public class MyLogging {
	// some codes in here
}
```

#### Declare Pointcut
Declaring pointcut in your aspect module with `@Pointcut` annotation.
``` java
@Aspect
public class MyLogging {
	
	@Pointcut("execution(* spring.test.aop.MyData.saveData(..))")
	private void saveDataPointcut() {
		
	}
}
```
In above codes, the pointcut is only work for *saveData* method of *MyData* class in the *spring.test.aop* package. To see the regular expression, go to [here](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/aop.html#aop-pointcuts-examples)

#### Delcare Advice
We can declare more types of advice using specific annotation in the aspect module, like `@Before`, `@After`, `@AfterReturning`, `@AfterThrowing`, `@Around`. Let's see the codes directly.

``` java 
@Aspect
public class MyLogging {

	@Pointcut("execution(* spring.test.aop.MyData.saveData(..))")
	private void saveDataPointcut() {

	}

	@Before("saveDataPointcut()")
	public void beforeSaveData() {
		System.out.println("The Data is going to be saved");
	}

	@After("saveDataPointcut()")
	public void afterSaveData() {
		System.out.println("The Data has been saved");
	}

	@Before("execution(* spring.test.aop.MyData.getData())")
	public void beforeGetData() {
		System.out.println("Ready to get the data");
	}

	@After("execution(* spring.test.aop.*.getData())")
	public void afterGetData() {
		System.out.println("The data has been got");
	}

	@AfterReturning(pointcut = "execution(* spring.test.aop.MyData.getData())", returning = "returnValue")
	public void afterReturningAdvice(Object returnValue) {
		System.out.println(String.format("The return value is: %s ",
				returnValue.toString()));
	}

	@AfterThrowing(pointcut = "execution(* spring.test.aop.*.concatData(..))", throwing = "ex")
	public void throwingAdvice(NullPointerException ex) {
		System.out.println(String.format("Throw exception is: %s",
				ex.getMessage()));
	}

	@Around("saveDataPointcut()")
	public void aroundAdvice(ProceedingJoinPoint point) throws Throwable {
		System.out.println("Saving Data....");

		point.proceed();

		System.out.println("Saved Data!");
	}

}
```
You can specify the pointcut name or pointcut expression to advice. whatever you want. One important thing is you must let the advice know which pointcut works for.

#### Simple Instance
``` java MyData.java
package spring.test.aop;

public class MyData {
	public String data;

	public void saveData(String data) {
		System.out.println("Saving the data: " + data);
		this.data = data;
	}

	public String getData() {
		System.out.println("Get the data: " + this.data);
		return this.data;
	}
	
	public void concatData(String data) {
		this.data.concat(data);
	}
}
```

``` java MyLogging.java
package spring.test.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;

@Aspect
public class MyLogging {

	@Pointcut("execution(* spring.test.aop.MyData.saveData(..))")
	private void saveDataPointcut() {

	}

	@Before("saveDataPointcut()")
	public void beforeSaveData() {
		System.out.println("The Data is going to be saved");
	}

	@After("saveDataPointcut()")
	public void afterSaveData() {
		System.out.println("The Data has been saved");
	}

	@Before("execution(* spring.test.aop.MyData.getData())")
	public void beforeGetData() {
		System.out.println("Ready to get the data");
	}

	@After("execution(* spring.test.aop.*.getData())")
	public void afterGetData() {
		System.out.println("The data has been got");
	}

	@AfterReturning(pointcut = "execution(* spring.test.aop.MyData.getData())", returning = "returnValue")
	public void afterReturningAdvice(Object returnValue) {
		System.out.println(String.format("The return value is: %s ",
				returnValue.toString()));
	}

	@AfterThrowing(pointcut = "execution(* spring.test.aop.*.concatData(..))", throwing = "ex")
	public void throwingAdvice(NullPointerException ex) {
		System.out.println(String.format("Throw exception is: %s",
				ex.getMessage()));
	}

	@Around("saveDataPointcut()")
	public void aroundAdvice(ProceedingJoinPoint point) throws Throwable {
		System.out.println("Saving Data....");

		point.proceed();

		System.out.println("Saved Data!");
	}

}
```
``` xml springConfig.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">
    
    <aop:aspectj-autoproxy/>
	
	<bean id="myLogging" class="spring.test.aop.MyLogging" />
	
	<bean id="myData" class="spring.test.aop.MyData" />
</beans>
```

``` java App.java
package spring.test.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

	private static ApplicationContext context;

	public static void main(String[] args) {
		context = new ClassPathXmlApplicationContext("springConfig.xml");

		MyData myData = (MyData) context.getBean("myData");

		myData.saveData("35");
		
		System.out.println("-----------------");
		myData.getData();
		
		System.out.println("-----------------");
		myData.concatData(null);
	}

}
```

Okay, run above simple instance. the output is below:

	Saving Data....
	The Data is going to be saved
	Saving the data: 35
	Saved Data!
	The Data has been saved
	-----------------
	Ready to get the data
	Get the data: 35
	The data has been got
	The return value is: 35 
	-----------------
	Throw exception is: null
	Exception in thread "main" java.lang.NullPointerException
	...