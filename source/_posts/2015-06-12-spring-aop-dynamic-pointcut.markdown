---
layout: post
title: "Spring AOP - Dynamic Pointcut"
date: 2015-06-12 16:23:30 +0800
comments: true
categories: [Java, Spring, AOP]
---
If we want to intercept methods with dynamic pointcut, we need to override *matches* and *getClassFilter* methods of **DynamicMethodMatcher** abstract class. The dynamic pointcut can verify the arguments of the method at runtime. This is a difference with static pointcut.

<!-- more -->
``` java MyDynamicPointcut.java
package spring.test.aop;

import java.lang.reflect.Method;

import org.springframework.aop.ClassFilter;
import org.springframework.aop.support.DynamicMethodMatcherPointcut;

public class MyDynamicPointcut extends DynamicMethodMatcherPointcut {

	@Override
	public boolean matches(Method method, Class<?> targetClass, Object[] args) {
		System.out.println("Dynamic check for " + method.getName());

		if (args.length > 0 && (int) args[0] > 10)
			return true;

		return false;
	}

	@Override
	public ClassFilter getClassFilter() {
		return new ClassFilter() {

			@Override
			public boolean matches(Class<?> clazz) {
				return clazz.equals(MyLogicClass.class);
			}

		};
	}

}

```
In above dynamic pointcut class, we verify the class name must be *MyLogicClass* and the value of the first argument in method must larger than 10, otherwise the method will not be intercepted.

``` java MyAroundAdvice.java
package spring.test.aop;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

public class MyAroundAdvice implements MethodInterceptor {

	@Override
	public Object invoke(MethodInvocation invocation) throws Throwable {
		System.out.println(">>>>>> Invoking method "
				+ invocation.getMethod().getName());

		Object result = invocation.proceed();

		System.out.println(">>>>>> Invoked Done");

		return result;
	}

}
```
We print some message before and after the invocation of method.

``` java MyLogicClass
package spring.test.aop;

public class MyLogicClass {

	public void methodOne() {
		System.out.println("MethodOne is Running");
	}

	public void methodTwo(int x) {
		System.out.println(String.format(
				"MethodTwo is running with argument %s", x));
	}
}
```

Here, in our logic class, there are two methods, one without argument, another with int type argument. Both method print message in console.

``` xml springConfig.xml
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN"
			"http://www.springframework.org/dtd/spring-beans-2.0.dtd">

<beans>
	<bean id="myAroundAdvice" class="spring.test.aop.MyAroundAdvice" />

	<bean id="myLogicClass" class="spring.test.aop.MyLogicClass" />

	<bean id="myDynamicPointcut" class="spring.test.aop.MyDynamicPointcut" />

	<bean id="advisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
		<property name="pointcut" ref="myDynamicPointcut" />
		<property name="advice" ref="myAroundAdvice" />
	</bean>

	<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="interceptorNames">
			<list>
				<value>advisor</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass" />
	</bean>
</beans>
```
Create our *MyDynamicPointcut* bean and *MyAroundAdvice* bean and put them into **DefaultPointcutAdvisor** bean. Finally put the advisor and target class to the **ProxyFactoryBean**.

``` java App.java
package spring.test.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {
	private static ApplicationContext context;

	public static void main(String[] args) {
		context = new ClassPathXmlApplicationContext("springConfig.xml");
		
		MyLogicClass proxy = (MyLogicClass) context.getBean("proxyFactoryBean");
		
		proxy.methodOne();
		System.out.println("\n--------------------\n");
		proxy.methodTwo(1);
		System.out.println("\n--------------------\n");
		proxy.methodTwo(11);
	
	}
}
```
We call the *methodTwo* twice with different arguments. Let's see the output.

	Dynamic check for methodOne
	MethodOne is Running

	--------------------

	Dynamic check for methodTwo
	MethodTwo is running with argument 1

	--------------------

	Dynamic check for methodTwo
	>>>>>> Invoking method methodTwo
	MethodTwo is running with argument 11
	>>>>>> Invoked Done
	
You see, only *methodTwo* with argument 11 (larger than 10) is intercepted.