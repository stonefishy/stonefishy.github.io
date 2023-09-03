---

title: "Spring AOP - Throws Advice"
date: 2015-06-08 14:39:43 +0800
comments: true
categories: Backend
tags: [Java, Spring]
---

In this type of Advice. This service executed when the logic method throws exceptions. To create Throws Advice, we should implement the interface called `ThrowsAdvice`.    

`ThrowsAdvice` is provided by _org.springframework.aop.*_ package. But it has not any method we need to override.  
<!-- more --> 

While creating a ThrowsAdvice class in spring AOP, we should implement our services in a method called `afterThrowing()` with 1 or 4 parameter(s). In fact, this method not given in `ThrowsAdvice` interface, but we should implement our services in afterThrowing() method only because when ever an Exception is occurred in the business logic then the IOC container internally calls afterThrowing() method to apply the services.   

Let's see the `afterThrowing()` method.

``` java 
package spring.test.aop;

import java.lang.reflect.Method;

import org.springframework.aop.ThrowsAdvice;

public class MyThrowsAdvice implements ThrowsAdvice {

	// just exception
	public void afterThrowing(Exception e) {

	}
	
	// specific null pointer exception
	public void afterThrowing(NullPointerException e) {

	}

	// with 4 parameters
	public void afterThrowing(Method method, Object[] args, Object target, Exception e) {

	}
}
```
If we write multiple afterThrowing methods in a class, then the `IOC` container will give the preference as below:

Specific exception > 4 parameters > just exception.   

Let's take a practice.

``` java MyLogicInterface.java
package spring.test.aop;

public interface MyLogicInterface {
	void divide(int a, int b);
}
```

``` java MyLogicClass.java
package spring.test.aop;

public class MyLogicClass implements MyLogicInterface {

	@Override
	public void divide(int a, int b) {
		System.out.println(String.format("%s divide %s is %s\n", a, b, a / b));
	}

}
```
In our logic class. We print the message about one param divided by another one.

``` java MyThrowsAdvice.java
package spring.test.aop;

import java.lang.reflect.Method;

import org.springframework.aop.ThrowsAdvice;

public class MyThrowsAdvice implements ThrowsAdvice {

	// just exception
	public void afterThrowing(Exception e) {
		System.out.println("Exception: " + e.getMessage());
	}
	
	// specific exception
	public void afterThrowing(ArithmeticException e) {
		System.out.println("Specific ArithmeticException: " + e.getMessage());
	}

	// with 4 parameters
	public void afterThrowing(Method method, Object[] args, Object target, Exception e) {
		System.out.println("With 4 parameters exception: " + e.getMessage());
	}
}
```

There have three implementations of afterThrowing method. And also we can add our handle exception logic in them.   

``` xml springConfig.xml
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN"
			"http://www.springframework.org/dtd/spring-beans-2.0.dtd">

<beans>
	<bean id="myThrowsAdvice" class="spring.test.aop.MyThrowsAdvice"/>
	
	<bean id="myLogicClass" class="spring.test.aop.MyLogicClass"/>
	
	<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="proxyInterfaces" value="spring.test.aop.MyLogicInterface"/>
		<property name="interceptorNames">
			<list>
				<value>myThrowsAdvice</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass"/>
	</bean>
</beans>
```

``` java App.java
package spring.test.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

	private static ApplicationContext applicationContext;

	public static void main(String[] args) {
		applicationContext = new ClassPathXmlApplicationContext("springConfig.xml");
		MyLogicInterface myLogicInterface = (MyLogicInterface)applicationContext.getBean("proxyFactoryBean");
		
		myLogicInterface.divide(10, 5);
		
		myLogicInterface.divide(10, 0);
	}

}
```
In our client, we can see the divide method is called twice. The First one will not throw exception, but second will throw ArithmeticException.    

Let's see the result of execution:

	10 divide 5 is 2

	Exception in thread "main" Specific ArithmeticException: / by zero
		
We can see the specific ArithmeticException is executed. Okay, let us remove the specific exception method and run it again.

	10 divide 5 is 2

	With 4 parameters exception: / by zero
	
The afterThrowing method with 4 parameters has been executed. Keep remove this method and run it.

	10 divide 5 is 2

	Exception: / by zero
	
So there is only one afterThrowing method is executed when there have multiple implementation.
