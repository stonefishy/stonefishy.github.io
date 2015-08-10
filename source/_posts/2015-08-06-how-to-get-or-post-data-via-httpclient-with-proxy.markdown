---
layout: post
title: "How to get or post data via HttpClient with proxy"
date: 2015-08-06 22:22:33 +0800
comments: true
categories: [Java]
---

The **HttpClient** is library which is support by apache. It can post or get data from specific  webservice and represents only the most basic contract for HTTP request execution.

We explain how to use this library with a example. In this example, we are going to use it to access the google tranlated API. Translating some text from specific language to other language. And this example using the Spring framework and Maven tool to build.

<!-- more -->

First, let us create our properties file which configures the google api url and some httpclient related stuff. Put it in our resource directory.

``` java
google.translation.api=https://www.googleapis.com/language/translate/v2
proxy.host=
proxy.port=
proxy.user=
proxy.password=
```

And then we create the util class which access the properties file.

``` java 
package org.httpclient.tutorial.utils;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.Properties;

/**
 * Created by yushi on 8/7/15.
 */
@Component
public final class ConstantUtil {

    private static final String PROXY_HOST = "proxy.host";
    private static final String PROXY_PORT = "proxy.port";
    private static final String PROXY_USER = "proxy.user";
    private static final String PROXY_PASSWORD = "proxy.password";
    private static final String GOOGLE_TRANSLATION_API ="google.translation.api";

    private Properties configurationProperties;


    @Autowired
    public ConstantUtil(Properties configurationProperties) {
        this.configurationProperties = configurationProperties;
    }

    public String getProxyHost() {
        return this.configurationProperties.getProperty(PROXY_HOST);
    }

    public int getProxyPort() {
        return Integer.parseInt(this.configurationProperties.getProperty(PROXY_PORT));
    }

    public String getProxyUser() {
        return this.configurationProperties.getProperty(PROXY_USER);
    }

    public String getProxyPassword() {
        return this.configurationProperties.getProperty(PROXY_PASSWORD);
    }

    public String getGoogleTranslationApi() {
        return  this.configurationProperties.getProperty(GOOGLE_TRANSLATION_API);
    }
}
```
So our configuration is completed. We also will create the HttpClient which can be used to access the webservice.

``` java
package org.httpclient.tutorial;

import org.apache.http.HttpHost;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.Credentials;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.client.CredentialsProvider;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.impl.client.HttpClients;
import org.httpclient.tutorial.utils.ConstantUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

/**
 * Created by yushi on 8/7/15.
 */
@Component
public final class HttpClientFactory {

    private ConstantUtil constantUtil;

    @Autowired
    public HttpClientFactory(ConstantUtil constantUtil) {

        this.constantUtil = constantUtil;
    }

    public CloseableHttpClient createHttpClient() {
        return HttpClients.createDefault();
    }

    public HttpClientBuilder createHttpClientWithProxy() {

        HttpHost proxy = new HttpHost(constantUtil.getProxyHost(), constantUtil.getProxyPort());
        Credentials credentials = new UsernamePasswordCredentials(constantUtil.getProxyUser(), constantUtil.getProxyPassword());
        AuthScope authScope = new AuthScope(constantUtil.getProxyHost(), constantUtil.getProxyPort());
        CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
        credentialsProvider.setCredentials(authScope, credentials);

        return HttpClients.custom().setDefaultCredentialsProvider(credentialsProvider).setProxy(proxy);
    }
}
```
In above codes, there are two methods, one is for default httpclient which is not need the proxy, another is httpclient with proxy.

So far, we have our own httpclient. let's create our own translation component with httpclient.

``` java
package org.httpclient.tutorial;

import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.util.EntityUtils;
import org.httpclient.tutorial.utils.ConstantUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

/**
 * Created by yushi on 8/7/15.
 */
@Component
public final class TranslateCore {
    private static final String KEY = "AIzaSyAFaWbO3r-9FNeHU6Obj5tm6wWSnIAlBkg";

    private HttpClientFactory httpClientFactory;
    private ConstantUtil constantUtil;

    @Autowired
    public TranslateCore(HttpClientFactory httpClientFactory, ConstantUtil constantUtil) {
        this.httpClientFactory = httpClientFactory;
        this.constantUtil = constantUtil;
    }

    public String translateContext(String sourceLanguage, String targetLanguage, String context) {
        String url = String.format("%s?key=%s&source=%s&target=%s&q=%s&", constantUtil.getGoogleTranslationApi(),
                KEY, sourceLanguage, targetLanguage, context);

        HttpGet httpGet = new HttpGet(url);
        CloseableHttpClient httpClient = httpClientFactory.createHttpClient();

        try {
            CloseableHttpResponse httpResponse = httpClient.execute(httpGet);
            String result = EntityUtils.toString(httpResponse.getEntity());

            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }

        return null;
    }
}
```
You will notice that KEY constant variable, which is support by google developer, you need to register google developer and then get you developer key.

Take your feet to [here](https://cloud.google.com/translate/v2/using_rest?hl=en)to see the google translate API usage.

Okay, then creating translation interface:

``` java
package org.httpclient.tutorial;

/**
 * Created by yushi on 8/7/15.
 */
public interface ITranslation {
    String translateToJapanese(String context);

    String translateToChinese(String context);

    String translate(String language, String context);
}
```
Let's implement our translation interfer, in this class, we just translat english to other language.

``` java 
package org.httpclient.tutorial;

/**
 * Created by yushi on 8/7/15.
 */

public class Translation implements ITranslation {
    public static final String ZH = "zh-CN";
    public static final String EN = "en";
    public static final String JA = "ja";

    private TranslateCore translateCore;

    public Translation(TranslateCore translateCore) {
        this.translateCore = translateCore;
    }

    @Override
    public String translateToJapanese(String context) {
        return translateCore.translateContext(EN, JA, context);
    }

    @Override
    public String translateToChinese(String context) {
        return translateCore.translateContext(EN, ZH, context);
    }

    @Override
    public String translate(String language, String context) {
        return translateCore.translateContext(EN, language, context);
    }
}
```

To run our application.
``` java 
package org.httpclient.tutorial;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;


/**
 * Created by yushi on 8/7/15.
 */
public class Application {

    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("application-context.xml");
        ITranslation translation = (ITranslation)context.getBean("translation");

        System.out.println(translation.translateToChinese("Hello"));
        System.out.println(translation.translateToJapanese("Hello"));

    }

}
```
We using the spring framework to create bean and run it. The spring configuration xml file is below.

``` java 
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <util:properties id="configurationProperties" location="classpath:configuration.properties"/>
    <context:component-scan base-package="org.httpclient.tutorial"/>

    <bean id="translation" class="org.httpclient.tutorial.Translation">
        <constructor-arg ref="translateCore"/>
    </bean>
</beans>
```
The output is below:

	{
 		"error": {
  		"errors": [
   		{
    		"domain": "usageLimits",
    		"reason": "dailyLimitExceeded",
    		"message": "Daily Limit Exceeded"
   		}
  		],
  		"code": 403,
  		"message": "Daily Limit Exceeded"
 		}
		}

This indicate our access the google translate API with httpclient is successful. But why response these text. Because you need to price to google and then are able to use this api.
To see the pricing of google translate API, visit [here](https://cloud.google.com/translate/v2/pricing).

