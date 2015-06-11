---
layout: post
title: "Spring AOP - Static Pointcut"
date: 2015-06-11 16:08:48 +0800
comments: true
categories: [Java, Spring, AOP]
---
Let's first to see the three very technical terms as below.

- Adivce - Indicate the action to take either before or after the method execution.
- Pointcut - Indicate which method should be intercept, by method name or regular expression pattern.
- Advisor - Group ‘Advice’ and ‘Pointcut’ into a single unit, and pass it to a proxy factory object
<!-- more -->
### No pointcut example

``` java MyLogicClass
package spring.test.aop;

public class MyLogicClass {

	public void logicMethodOne() {
		System.out.println("This is from logicMethodOne");
	}
	
	public void logicMethodTwo() {
		System.out.println("This is from logicMethodTwo");
	}
	
	public void testLogic() {
		System.out.println("This is from testLogic");
	}
}

```

``` java MyAroundAdvice.java
package spring.test.aop;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

public class MyAroundAdvice implements MethodInterceptor {

	@Override
	public Object invoke(MethodInvocation invocation) throws Throwable {
		System.out.println("Before method execuation");

		Object result = invocation.proceed();
		
		System.out.println("After method execuation");

		return result;
	}

}

```

``` java App.java
package spring.test.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

	private static ApplicationContext applicationContext;

	public static void main(String[] args) {

		applicationContext = new ClassPathXmlApplicationContext("springConfig.xml");
		
		MyLogicClass logicClass = (MyLogicClass)applicationContext.getBean("proxyFactoryBean");

		System.out.println("\n--------------------\n");
		logicClass.logicMethodOne();
		
		System.out.println("\n--------------------\n");
		logicClass.logicMethodTwo();
		
		System.out.println("\n--------------------\n");
		logicClass.testLogic();
		System.out.println("\n--------------------\n");

	}

}
```

``` xml
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN"
			"http://www.springframework.org/dtd/spring-beans-2.0.dtd">

<beans>
	<bean id="myAroundAdvice" class="spring.test.aop.MyAroundAdvice"/>
	
	<bean id="myLogicClass" class="spring.test.aop.MyLogicClass"/>
	
	<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="interceptorNames">
			<list>
				<value>myAroundAdvice</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass"/>
	</bean>
</beans>
```

``` text output
--------------------

Before method execuation
This is from logicMethodOne
After method execuation

--------------------

Before method execuation
This is from logicMethodTwo
After method execuation

--------------------

Before method execuation
This is from testLogic
After method execuation

--------------------
```

In above example, all logic methods of the class has been intercepted due to the application doesn't use the pointcut to specific.

### Name match example
And now, we just want to intercept the method *testLogic*, so we need to create **NameMatchMethodPointcut** bean in spring config xml. and put the method name you want to intercept to the **mappedName** property value.

``` xml
<bean id="nameMatchPointcut" class="org.springframework.aop.support.NameMatchMethodPointcut">
		<property name="mappedName" value="testLogic"/>
</bean>
```

And also you can mapped multiple methods with **mappedNames**, like below.

``` xml
<bean id="nameMatchPointcut" class="org.springframework.aop.support.NameMatchMethodPointcut">
		<property name="mappedNames">
			<list>
				<value>logicMethodOne</value>
				<value>logicMethodTwo</value>
			</list>
		</property>
</bean>
```


Then create **DefaultPointcutAdvisor** bean to group and associate the pointcut and advice.

``` xml
<bean id="myPointcutAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
		<property name="pointcut" ref="nameMatchPointcut"/>
		<property name="advice" ref="myAroundAdvice"/>
</bean>
```

Replace the **interceptorNames** of **ProxyFactoryBean** with the new Advisor.

``` xml
<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="interceptorNames">
			<list>
				<value>myPointcutAdvisor</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass"/>
</bean>
```

It's done, let's see the full spring config file and output.

``` xml springConfig.xml
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN"
			"http://www.springframework.org/dtd/spring-beans-2.0.dtd">

<beans>
	<bean id="myAroundAdvice" class="spring.test.aop.MyAroundAdvice"/>
	
	<bean id="myLogicClass" class="spring.test.aop.MyLogicClass"/>
	
	<bean id="nameMatchPointcut" class="org.springframework.aop.support.NameMatchMethodPointcut">
		<property name="mappedName" value="testLogic"/>
		<!-- <property name="mappedNames">
			<list>
				<value>logicMethodOne</value>
				<value>logicMethodTwo</value>
			</list>
		</property> -->
	</bean>
	
	<bean id="myPointcutAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
		<property name="pointcut" ref="nameMatchPointcut"/>
		<property name="advice" ref="myAroundAdvice"/>
	</bean>
	
	<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="interceptorNames">
			<list>
				<value>myPointcutAdvisor</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass"/>
	</bean>
</beans>
```

``` text output
--------------------

This is from logicMethodOne

--------------------

This is from logicMethodTwo

--------------------

Before method execuation
This is from testLogic
After method execuation

--------------------
```
### Regular expression example
Also we can use regular expression to match methods which should be intercepted. Create **RegexpMethodPointcutAdvisor** bean and set the *pattern* property value with regular expression and combine with advice.

``` xml 
<bean id="regexPointcutAdvisor" class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
		<property name="pattern" value=".*Method.*"/>
		
		<property name="advice" ref="myAroundAdvice"/>
</bean>
```
And you can specific multiple patterns with *patterns* property.

``` xml 
<bean id="regexPointcutAdvisor" class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
		<property name="patterns">
			<list>
				<value>.*Method.*</value>
			</list>
		</property>
		
		<property name="advice" ref="myAroundAdvice"/>
</bean>
```
Finally pass the advisor to the **ProxyFactoryBean**.

``` xml
<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="interceptorNames">
			<list>
				<value>regexPointcutAdvisor</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass"/>
</bean>
```

``` text output
--------------------

Before method execuation
This is from logicMethodOne
After method execuation

--------------------

Before method execuation
This is from logicMethodTwo
After method execuation

--------------------

This is from testLogic

--------------------
```

In practice, you can use it to manage DAO layer, where you can declare “.\*DAO.*” to intercept all your DAO classes to support transaction.