---

title: "how to use jdbc in spring"
date: 2015-05-05 16:31:53 +0800
comments: true
categories: Backend
tags: [Java, Spring]
---

As we know, we can use `jdbc` to access database in java programming. And now let us use `jdbc` in spring. The `spring framework` reduces developer handlers any accessing database exception, because it deal with them internally. The spring framework integrated the jdbctemplate, they are `org.springframework.jdbc.datasource.DriverManagerDataSource` and `org.springframework.jdbc.core.JdbcTemplate`.   

And here we use the `hsqldb` to act as our memory database. You can go to [here](http://hsqldb.org/) to look at it. we use the `Maven` tools to management our project. the dependencies is:    

<!-- more -->
``` 
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
			<artifactId>spring-jdbc</artifactId>
			<version>4.1.6.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.hsqldb</groupId>
			<artifactId>hsqldb</artifactId>
			<version>1.8.0.10</version>
		</dependency>
</dependencies>
```
The spring configure file:    
 
```
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN"
			"http://www.springframework.org/dtd/spring-beans-2.0.dtd">
			
<beans>

 	<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	    <property name="driverClassName" value="org.hsqldb.jdbcDriver"/>
	    <property name="url" value="jdbc:hsqldb:mem:MyDB"/>
	    <property name="username" value="sa"/>
	    <property name="password" value=""/>
 	</bean>
 	
 	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		 <constructor-arg>
		   	<ref bean="dataSource"/>
		 </constructor-arg>    
 	</bean>
 	
    <bean id="createTable" class="java4s.SpringJdbcCreateTable">
   		<property name="jt">
      		<ref bean="jdbcTemplate"/>
   		</property>
 	</bean>
 
</beans>  
```  

we have a SpringJdbcCreateTable class to accesss database, this class has `JdbcTemplate` property,    
    
``` java
JdbcTemplate jt;

public void setJt(JdbcTemplate jt) {
	this.jt = jt;
}
```
we use spring bean to inject value to this property. And you will see it in above spring configure file.

create table, the `execute` method doesn't return value.      

``` java 
public void createTable() {
	jt.execute("CREATE TABLE user(id int, name varchar(10), age int)");
	// execute() returns void
	System.out.println("table created");
}
```

insert data, using the `update` method to insert data, this method will return how many rows have been inserted. 

``` java
public void insertData() {
	int k = jt.update("INSERT INTO user VALUES (99, 'user_99', 49)");
	showDatas();
		
	System.out.println(String.format("%s row inserted", k));
}
```
update data, using the `update` method to update data, it will return how many rows are affected.

``` java
public void updateData() {
	int k = jt.update("UPDATE user set name='user_new' where name='user_99'");
	showDatas();
		
	System.out.println(String.format("%s row updated", k));
}
```

delete data, also using the `update` method.

``` java
public void deleteData() {
	int k = jt.update("DELETE from user where name='user_new'");
		
	showDatas();
	System.out.println(String.format("%s row deleted", k));
}
```

query data, the jdbctemplate has many query interfaces. Here we use `queryForRowSet` method to get the data by condition.

``` java

private void showDatas() {
	System.out.println("|  ID |  NAME    |  AGE  |");
	SqlRowSet rowSet = jt.queryForRowSet("SELECT * FROM user");

	while (rowSet.next()) {
		int id = rowSet.getInt(1);
		String name = rowSet.getString(2);
		int age = rowSet.getInt(3);

		String msg = String
				.format("|  %s  |  %s  |  %s   |", id, name, age);
			
		System.out.println(msg);
	}		
}
```
okay, to so far, let us call them in main entrance.

``` java
public static void main(String args[])
{
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spconfig.xml");
	SpringJdbcCreateTable st =(SpringJdbcCreateTable)applicationContext.getBean("createTable");
		
	st.createTable();
	st.initSomeDatas();
	st.insertData();
	st.updateData();
	st.deleteData();
}
```

Look, it's very easy to access database with spring framework.