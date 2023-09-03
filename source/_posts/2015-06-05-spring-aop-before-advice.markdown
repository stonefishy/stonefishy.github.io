---

title: "Spring AOP - Before Advice"
date: 2015-06-05 15:33:00 +0800
comments: true
categories: Backend
tags: [Java, Spring]
---
As we know the `Aspect` is just the name of the `cross-cutting` functionality, not the implementation. So what's the implementation of the `cross-cutting` functionality? It's called `Advice`. An `Advice` provides the code for implementation of the service. It is like *Logging* service, *Logging* is a `Aspect` and `Advice` denotes the implementation of *Log4j*.   
<!-- more -->
There are four types of `Adivce`:

- **Before Advice**
- After Advice
- Around Advice
- Throws Advice         

In this article, we only explain `Before Advice`. The other advices we will talk about it in later blogs.   

The `Before Advice` is the implementation of the service. It will be applied before the business logic of method is going to execute. So this means this advice will execute before the business logic. we can use it to do something like authentication before the business logic execution.    

It will be applied in the runtime of application, not the compilation of the code. And also it will generate a new *proxy* class which is combined by this advice and business logic class in the runtime automatically.   

When we create a `Before Advice`, we should implement the `MethodBeforeAdvice` interface, this interface is given by _org.springframework.aop.*_ package. And then we need to override the `before` method of it.    

``` java
package spring.test.aop;

import java.lang.reflect.Method;

import org.springframework.aop.MethodBeforeAdvice;

public class MyBeforeAdvice implements MethodBeforeAdvice {

	@Override
	public void before(Method method, Object[] args, Object target)
			throws Throwable {
		// TODO Auto-generated method stub
		
	}

}
```
There are 3 parameters in the *before* method.   

The first parameter *`Method` method* is used to access the method name of the business logic via `getName()`.    

The second parameter *Object[] args* is Object array, it is used to access the arguments of the logic method.    

The last parameter *Object target* is an object to whom this service will be going to apply, usually this will taken care by *container*, actually we no need to care it.     

Okay, let's see a full example.

``` xml
<dependencies>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>4.1.6.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>4.1.6.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
			<version>4.1.6.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>4.1.6.RELEASE</version>
		</dependency>
	</dependencies>
```

In the pom.xml file, we add above dependencies to our project. (this project use maven to manage)   

Okay, let us create our logic class, firstly we need to create interface for it.

``` java
package spring.test.aop;

public interface MyLogicInterface {
	void add(int a, int b);
	
	void subtract(int a, int b);
}
```

We created two method in this interface, this interface is very important for our `AOP` project.

``` java
package spring.test.aop;

public class MyLogicClass implements MyLogicInterface {

	@Override
	public void add(int a, int b) {
		String result = String.format("%s + %s = %s", a, b, a + b);
		System.out.println(result);
	}

	@Override
	public void subtract(int a, int b) {
		String result = String.format("%s - %s = %s", a, b, a - b);
		System.out.println(result);
	}

}
```
Our class implements the interface, just print the result of the add or subtract.

For now, we have our logic class. but now we want to do something before the method logic is going to execute. So the `Before Advice` is comming.

``` java
package spring.test.aop;

import java.lang.reflect.Method;

import org.springframework.aop.MethodBeforeAdvice;

public class MyBeforeAdvice implements MethodBeforeAdvice {

	@Override
	public void before(Method method, Object[] args, Object target) throws Throwable {
		System.out.println(String.format("This will be executed before the %s method", method.getName()));
	}
}

```
We print some messages in our `Before Advice`, this will be printed before the logic method result.

Finally, we need to make the logic class and our `Before Advice` are combined together. so we need to config them in the xml file.

``` xml
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN"
			"http://www.springframework.org/dtd/spring-beans-2.0.dtd">

<beans>
	<bean id="myBeforeAdvice" class="spring.test.aop.MyBeforeAdvice"/>
	
	<bean id="myLogicClass" class="spring.test.aop.MyLogicClass"/>
	
	<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="proxyInterfaces" value="spring.test.aop.MyLogicInterface"/>
		<property name="interceptorNames">
			<list>
				<value>myBeforeAdvice</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass"/>
	</bean>
</beans>
```
In `AOP` always we need to create a spring bean in the form of interface and implementation class only, because the IOC container internally creates proxy class by implementing that interface with the help of `ProxyFactoryBean`.
To run this application, we need a class which with *main* method. See below codes.

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
In the client application, we are passing id of `ProxyFactoryBean` (proxyFactoryBean) to get the object, because we need proxyed object to invoke our logic method.

The result of the execution is below:   

	This will be executed before the add method
	10 + 20 = 30
	This will be executed before the subtract method
	10 - 5 = 5
	
So, it is simple to use it. and we separated the logic and the services completely, only need to config the xml file when the services changed.