---
layout: post
title: "Spring AOP - After Advice"
date: 2015-06-06 10:14:46 +0800
comments: true
categories: [Java, Spring, AOP]
---
`Advice` is the implementation of the `cross-cutting` functionality. An `Adivce` provides the code for implementation of the service.

There are four types of Advice:    

- Before Advice
- **After Advice**
- Around Advice
- Throws Advice
<!-- more -->
`After Advice` is same as `Before Advice`. But in order to create our after advice in spring, our class should implement an interface called `AfterReturningAdvice`, this interface is given in _org.springframework.aop.*_ package. And we need to override a method given by this interface called `afterReturning()`.   

``` java
public class MyAfterAdvice implements AfterReturningAdvice {

	@Override
	public void afterReturning(Object returnValue, Method method,
			Object[] args, Object target) throws Throwable {
		// TODO Auto-generated method stub
		
	}

}
``` 
The *returnValuef* is the return value of the logic method, we can use it to access the return of the logic function. The *method* parameter is the logic method, we can use *getName()* to access the name of method. And the *args* parameter is arguments of logic method.   

Let's see full example.

``` java 
package spring.test.aop;

public interface MyLogicInterface {
	int add(int a, int b);
	
	int subtract(int a, int b);
}

```
In this interface, we changed the method's signature, we have return value for both two method.   

``` java
package spring.test.aop;

public class MyLogicClass implements MyLogicInterface {

	@Override
	public int add(int a, int b) {
		return a + b;
	}

	@Override
	public int subtract(int a, int b) {
		return a - b;
	}

}

```
In *MyLogicClass*, we implemented the two methods *add* and *subtract*, and return the value.
``` java
package spring.test.aop;

import java.lang.reflect.Method;

import org.springframework.aop.AfterReturningAdvice;

public class MyAfterAdvice implements AfterReturningAdvice {

	@Override
	public void afterReturning(Object returnValue, Method method,
			Object[] args, Object target) throws Throwable {

		String message = String.format(
				"The result from method %s(%s, %s) is: %s", method.getName(),
				args[0], args[1], returnValue);

		System.out.println(message);

	}

}
```
We print all the messages of the logic method in our `After Advice`, include method name, the return value of method, the arguments etc.   

Okay, let's see our spring config file.

``` java
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN"
			"http://www.springframework.org/dtd/spring-beans-2.0.dtd">

<beans>
	<bean id="myAfterAdvice" class="spring.test.aop.MyAfterAdvice"/>
	
	<bean id="myLogicClass" class="spring.test.aop.MyLogicClass"/>
	
	<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="proxyInterfaces" value="spring.test.aop.MyLogicInterface"/>
		<property name="interceptorNames">
			<list>
				<value>myAfterAdvice</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass"/>
	</bean>
</beans>
```
It is same with last spring config file of `Before After`. We just use *MyAfterAdvice* to instead of *MyBeforeAdvice*.   

To make this application run, we should read spring config file.   

``` java
package spring.test.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

	private static ApplicationContext applicationContext;

	public static void main(String[] args) {
		applicationContext = new ClassPathXmlApplicationContext("springConfig.xml");
		MyLogicInterface myLogicInterface = (MyLogicInterface)applicationContext.getBean("proxyFactoryBean");
		
		myLogicInterface.add(10, 20);
		myLogicInterface.subtract(10, 5);
	}

}

```

The result of the application executioin.

	The result from method add(10, 20) is: 30
	The result from method subtract(10, 5) is: 5