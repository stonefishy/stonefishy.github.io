---

title: "Spring AOP - XML Schema"
date: 2015-06-14 15:48:55 +0800
comments: true
categories: Backend
tags: [Java, Spring]
---
Previously, we created AOP with Spring framework via implementing interface of spring AOP. And now we can use XML Schema to achieve it. We need to add below **AspectJ** libraries to our project. So downloading and adding them to the *CLASSPATH* of application,  and also we can use the maven to build our application.    
 - *aspectjrt.jar*   
 - *aspectjweaver.jar*   
 - *aopalliance.jar*   
<!-- more -->
Let's see our pom.xml
``` xml pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>Spring-AOP-XML-Schema</groupId>
	<artifactId>Spring-AOP-XML-Schema</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<build>
		<sourceDirectory>src</sourceDirectory>
		<plugins>
			<plugin>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.1</version>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
				</configuration>
			</plugin>
		</plugins>
	</build>
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
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>1.8.6</version>
		</dependency>
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>1.8.6</version>
		</dependency>
		<dependency>
			<groupId>aopalliance</groupId>
			<artifactId>aopalliance</artifactId>
			<version>1.0</version>
		</dependency>
	</dependencies>
</project>
```

Okay, so we start to config the AOP with xml schema. 

1. Declaring Aspect:

``` xml
<aop:config>
	<aop:aspect id="logging" ref="myLogging">
	</aop:aspect>
</aop:config>
	
<bean id="myLogging" class="spring.test.aop.MyLogging" />
```
Create our service bean called *myLogging*. this service should be injected before or after in the logic method. And use `<aop:aspect>` to reference it.

2. Declaring Pointcut:

``` xml
<aop:config>
	<aop:aspect id="logging" ref="myLogging">
		<aop:pointcut id="saveDataPointcut" expression="execution(* spring.test.aop.*.saveData(..))" />
			<aop:pointcut id="getDataPointcut" expression="execution(* spring.test.aop.*.getData())"/>
	</aop:aspect>
</aop:config>
	
<bean id="myLogging" class="spring.test.aop.MyLogging" />

<bean id="myData" class="spring.test.aop.MyData" />
```   

Create logic class bean and pointcut with `<aop:pointcut>` , define the expression matcher to match the methods which will be executed with AOP. To see the expression of pointcut, go to [http://docs.spring.io/spring/docs/current/spring-framework-reference/html/aop.html](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/aop.html).

3. Declaring Advice:

``` xml
<aop:config>
	<aop:aspect id="logging" ref="myLogging">
		
		<aop:pointcut id="saveDataPointcut" expression="execution(* spring.test.aop.*.saveData(..))" />
		
		<aop:pointcut id="getDataPointcut" expression="execution(* spring.test.aop.*.getData())"/>
			
		<aop:before method="beforeSaveData" pointcut-ref="saveDataPointcut" />
			
		<aop:after method="afterSaveData" pointcut-ref="saveDataPointcut" />
			
		<aop:before method="beforeGetData" pointcut="execution(* spring.test.aop.MyData.getData())"/>
			
		<aop:after method="afterGetData" pointcut="execution(* spring.test.aop.*.get*())"/>
			
		<aop:after-returning method="afterReturningAdvice"
				pointcut-ref="getDataPointcut" returning="returnValue" />
				
		<aop:after-throwing method="throwingAdvice"
				pointcut="execution(* spring.test.aop.MyData.concatData(String))" throwing="ex" />
	</aop:aspect>
</aop:config>
	
<bean id="myLogging" class="spring.test.aop.MyLogging" />
	
<bean id="myData" class="spring.test.aop.MyData" />
```

Use `<aop:before>`, `<aop:after>`, `<aop:after-returning>` and `<aop:after-throwing>` to create each advices. Reference to pointcut bean and appoint which method of service can be injected. Note, for *after-returning* and *after-throwing*, we should add attributes *returning* and *throwing* to access their variables.   

The full configration file is: 

``` xml springConfig.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

	<aop:config>
		<aop:aspect id="logging" ref="myLogging">
		
			<aop:pointcut id="saveDataPointcut" expression="execution(* spring.test.aop.*.saveData(..))" />
			
			<aop:pointcut id="getDataPointcut" expression="execution(* spring.test.aop.*.getData())"/>
			
			<aop:before method="beforeSaveData" pointcut-ref="saveDataPointcut" />
			
			<aop:after method="afterSaveData" pointcut-ref="saveDataPointcut" />
			
			<aop:before method="beforeGetData" pointcut="execution(* spring.test.aop.MyData.getData())"/>
			
			<aop:after method="afterGetData" pointcut="execution(* spring.test.aop.*.get*())"/>
			
			<aop:after-returning method="afterReturningAdvice"
				pointcut-ref="getDataPointcut" returning="returnValue" />
				
			<aop:after-throwing method="throwingAdvice"
				pointcut="execution(* spring.test.aop.MyData.concatData(String))" throwing="ex" />
				
		</aop:aspect>
	</aop:config>
	
	<bean id="myLogging" class="spring.test.aop.MyLogging" />
	
	<bean id="myData" class="spring.test.aop.MyData" />
</beans>
```

From now, the AOP configration completed. Let's see our Logging service and Logic class.

``` java MyLogging.java
package spring.test.aop;

public class MyLogging {

	public void beforeSaveData() {
		System.out.println("The Data is going to be saved");
	}

	public void afterSaveData() {
		System.out.println("The Data has been saved");
	}
	
	public void beforeGetData() {
		System.out.println("Ready to get the data");
	}
	
	public void afterGetData() {
		System.out.println("The data has been got");
	}

	public void afterReturningAdvice(Object returnValue) {
		System.out.println(String.format(
				"The return value is: %s ", returnValue.toString()));
	}

	public void throwingAdvice(NullPointerException ex) {
		System.out.println(String.format(
				"Throw exception is: %s", ex.getMessage()));
	}

}
```
In our Logging service, the *afterReturningAdvice* and *throwingAdvice* methods should take *returnValue* which is Object type and Exception type *ex* paramters.  

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

We use the different pointcut to decide which service method can be injected which logic method.   

Finally, let's get our main class:

``` java App.java
package spring.test.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

	private static ApplicationContext context;

	public static void main(String[] args) {
		context = new ClassPathXmlApplicationContext("springConfig.xml");

		MyData myData = (MyData) context.getBean("myData");

		myData.saveData("This is my data");
		
		System.out.println("-----------------");
		myData.getData();
	}

}
```

See the output of application:

	he Data is going to be saved
	Saving the data: This is my data
	The Data has been saved
	-----------------
	Ready to get the data
	Get the data: This is my data
	The return value is: This is my data 
	The data has been got
	-----------------
	Throw exception is: null
	Exception in thread "main" java.lang.NullPointerException
	at java.lang.String.concat(String.java:1970)
	at spring.test.aop.MyData.concatData(MyData.java:17)
	at spring.test.aop.MyData$$FastClassBySpringCGLIB$$731fbdfd.invoke(<generated>)
	at org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:204)
	at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.invokeJoinpoint(CglibAopProxy.java:717)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:157)
	at org.springframework.aop.aspectj.AspectJAfterThrowingAdvice.invoke(AspectJAfterThrowingAdvice.java:58)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:179)
	at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:92)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:179)
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:653)
	at spring.test.aop.MyData$$EnhancerBySpringCGLIB$$5a76fbda.concatData(<generated>)
	at spring.test.aop.App.main(App.java:21)
