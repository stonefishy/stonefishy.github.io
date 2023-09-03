---

title: "JSP - Custom Tag"
date: 2014-12-01 21:30:41 +0800
comments: true
categories: Backend
tags: [Java, JSP]
---
Custom Tag is a user-defined JSP language element. When it is contained in JSP page and it will translate into a servlet, the custom tag is converted to opertions on an object called a tag handler. The web container then invokes those operations when the JSP page's servlet is executed.   

If we want to create a custom tag, what we need to do is simply extend `SimpleTagSupport` class and override the `doTag()` method, where you can place your code to generate content for the tag.   

Let's getting started to create a custom tag now.   
As you can see below, consider we want to create two tag, one is user tag with name and isMale attribute, another is system tag with size attribute.

``` jsp
<custom:user name="user1" isMale="true"/>
<custom:system size="1024"/>
```
<!-- more -->
So firstly what we need to do is create two java classes that act as a tag handler for each other tag. and them must extends `SimpleTagSupport` class.   

``` java UserTag.java
package com.jsp.custom.tag;

import javax.servlet.jsp.JspException;
import javax.servlet.jsp.JspWriter;
import javax.servlet.jsp.tagext.SimpleTagSupport;
import java.io.IOException;
import java.io.StringWriter;
import java.util.Date;

/**
 * Created by yushi on 12/1/14.
 */
public class UserTag extends SimpleTagSupport {
    private String name;
    private Boolean isMale;
    private Date birthday;

    private StringWriter sw = new StringWriter();

    public void doTag() throws JspException, IOException
    {
        JspWriter out = getJspContext().getOut();

        // print name info with name attribute
        out.println(name);

        // print gender info via isMale attribute
        out.println(isMale ? "I'm male!" : "I'm female!");

        if (birthday != null) {
            out.println(String.format("My birthday is %s", birthday.toString()));
        }

        // print body text with body content
        getJspBody().invoke(sw);
        getJspContext().getOut().println(sw.toString());
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setIsMale(Boolean isMale) {
        this.isMale = isMale;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
}
``` 

``` java SystemTag.java
package com.jsp.custom.tag;

import javax.servlet.jsp.tagext.SimpleTagSupport;
import java.io.IOException;

/**
 * Created by yushi on 12/2/14.
 */
public class SystemTag extends SimpleTagSupport {
    private int size;

    public void doTag() throws IOException {
        getJspContext().getOut().println(String.valueOf(String.format("The system's size is: %s", String.valueOf(size))));
    }

    public void setSize(int size) {
        this.size = size;
    }
}
```

Above two class just override the `doTag()` method and take the current `JspContext` object using `getJspContext()` method, and then send message content to the current `JspWriter` object.   

Secondly we need to create tld extention file. It's a tag library file. let us see the codes.    
``` xml customTag.tld
<taglib>
    <tlib-version>1.0</tlib-version>
    <jsp-version>2.0</jsp-version>
    <short-name>custom tld</short-name>
    <tag>
        <name>user</name>
        <tag-class>com.jsp.custom.tag.UserTag</tag-class>
        <body-content>tagdependent</body-content>
        <attribute>
            <name>name</name>
            <required>true</required>
            <description>user name</description>
            <!-- the type default is String if it is not specified -->
            <!--<type>java.lang.String</type>-->
        </attribute>
        <attribute>
            <name>isMale</name>
            <required>false</required>
            <description>the use is male or not male</description>
            <type>java.lang.Boolean</type>
        </attribute>
        <attribute>
            <name>birthday</name>
            <type>java.util.Date</type>
        </attribute>
    </tag>
    <tag>
        <name>system</name>
        <tag-class>com.jsp.custom.tag.SystemTag</tag-class>
        <body-content>empty</body-content>
        <attribute>
            <name>size</name>
            <required>false</required>
            <description>the size of system</description>
        </attribute>
    </tag>
</taglib>
```

Finally, we can use these both tags in our JSP page.

``` jsp
<%@ taglib prefix="custom" uri="WEB-INF/customTag.tld" %>
<html>
    <head>
        <title>custom tag training</title>
    </head>
    <body>
        <custom:user name="user1" isMale="true"/>
        <custom:system size="1024"/>
    </body>
</html>
```

