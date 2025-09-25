---
title: The Intelligent SQL Generator base on Spring AI with AWS Bedrock
date: 2024-10-23 15:01:12
tags: [Spring, Spring AI, AI, AWS, Java, SQL]
categories: AI/ML
top: true
swiper: true
swiperImg: "/medias/bg-images/v2-d61b1af36a619fec1b32775d75f4900d_1440w.webp"
---

The generative AI is a type of artificial intelligence (AI) that can learn from data and generate new data. In this article, we will discuss how to build an intelligent SQL generator using `Spring AI` and `AWS Bedrock`. For example, the application able to provide the data sql to us after we input the natural language questions, and we can query the data by using the sql, even display the chart base on data queried.

## Spring AI
The `Spring AI` is a project of `Spring`. It support for all major AI Model providers such as `Anthropic`, `OpenAI`, `Microsoft`, `Amazon`, `Google`, and `Ollama`. Model type supports such as `Chart Completion`, `Text to Image`, `Text to Speech`, `Translation`, `Audio Transcription` and so on. It make it easy to integrate AI models into the application.

{% image /assets/images/ai-ml/spring-ai.png, alt="Spring AI", width="500px" %}

## AWS Bedrock
Amazon Bedrock is a fully managed service that makes FMs from leading AI startups and Amazon available via an API, so you can choose from a wide range of FMs to find the model that is best suited for your use case. It contains `Anthropic` (`Claude`), `Meta` (`Llama`) and `Stability AI` models. In this blog, we will use `Claude` AI model of `Anthropic` to build our intelligent SQL generator.

{% image /assets/images/ai-ml/aws-bedrock-ai.png, alt="AWS Bedrock", width="600px" %}

## Building Intelligent SQL Generator
Assuming we're data analyzer, we have product_sales, products and customers three tables data in mysql. And we want to query the data by input natural language instead of write specific SQL manually. We can use AI to understand user natural langauge to generate the SQL base on table schemas. Let's get start. 

### Create a new Spring Boot project
Create a `Spring Boot` project with restful api, add the following dependencies in maven `pom.xml`

``` xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-bedrock-ai-spring-boot-starter</artifactId>
    </dependency>
```

The `spring-ai-bedrock-ai-spring-boot-starter` is library that provides integration with `AWS Bedrock` models in `Spring AI`.

### Configure AWS Bedrock configuraitons
In application.properties, configur below configurations.

``` text
spring.application.name=spring-ai-datasql
spring.ai.bedrock.aws.region=us-east-1
spring.ai.bedrock.aws.timeout=5m
spring.ai.bedrock.anthropic3.chat.enabled=true
spring.ai.bedrock.anthropic3.chat.options.max-tokens=4000

# config below AWS credential key, configure it in Java environments or System environments
spring.ai.bedrock.aws.access-key=${AWS_ACCESS_KEY_ID}
spring.ai.bedrock.aws.secret-key=${AWS_SECRET_ACCESS_KEY}
```

### Prepare prompts for AI
Since we only need AI to generate the SQL base on mysql table schema. So we need prepare prompts to `AI` to fully understand our requirements. Below is `prompts.txt`

``` text
There are 3 mysql tables schema product_sales, products, customers.
CREATE TABLE product_sales (
    sale_id INT AUTO_INCREMENT PRIMARY KEY,
    product_id INT NOT NULL,
    sale_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    price DECIMAL(10, 2) NOT NULL,
    customer_id INT,
    region VARCHAR(100),
    FOREIGN KEY (product_id) REFERENCES products(product_id),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100),
    product_category VARCHAR(100)
);
CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_name VARCHAR(100)
);
I will ask you question, please base on table schema to generate the SQL text in single line, please note I only need full correct sql text, do not
need other text or any other characters.
```

In above prompts, you can see it tells AI we only need SQL text base on the 3 mysql table schemas.

### Core Code
Create a restful controller and pass the prompts and user input message to AI model.

```java 
package spring.ai.datasql.controller;

import org.springframework.ai.bedrock.anthropic3.BedrockAnthropic3ChatModel;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import spring.ai.datasql.service.PromptService;

import java.io.IOException;
import java.util.Map;

@RestController
public class SQLGenController {
    private final BedrockAnthropic3ChatModel chatModel;

    @Autowired
    private PromptService prompts;

    @Autowired
    public SQLGenController(BedrockAnthropic3ChatModel chatModel) {
        this.chatModel = chatModel;
    }

    @PostMapping("/ai/sql")
    public Map generate(@RequestBody String message) throws IOException {
        String newMsg = prompts.getContent() + message;
        return Map.of("sql", chatModel.call(newMsg));
    }
}
```

In above code, we inject `BedrockAnthropic3ChatModel` which is `Anthropic` model of `AWS Bedrock` provided by `Spring AI`. We also inject `PromptService` which is a service to read prompts.
In `generate` method, we read prompts from `PromptService` and append user input message to it. Then we call `chatModel.call` method to generate the SQL text.

Code of `PromptService` to read prompts from file.

``` java
package spring.ai.datasql.service;
import jakarta.annotation.PostConstruct;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;

@Service
public class PromptService {

    @Value("classpath:prompts.txt")
    private org.springframework.core.io.Resource resource;

    private String fileContent;

    @PostConstruct
    public void init() throws IOException {
        fileContent = new String(Files.readAllBytes(Paths.get(resource.getURI())));
    }

    public String getContent() {
        return fileContent;
    }
}
```

### Run the application
Run the application and test the API by sending a request with user input message. We may encounter below errors.

{% image /assets/images/ai-ml/aws-bedrock-model-can-not-access.png, alt="AWS Bedrock Model can not access" %}

This is because the `spring.ai.bedrock.anthropic3.chat.model` in  current Spring AI version default value is `anthropic.claude-3-sonnet-20240229-v1:0`. Let's check the anthropic available Claude models in AWS Bedrock. Here we use `Claude 3.5` AI model.

{% image /assets/images/ai-ml/aws-bedrock-claude-model-id.png, alt="AWS Bedrock Anthropic Claude Model Id" %}

Copy this model id and update the `spring.ai.bedrock.anthropic3.chat.model` in `application.properties` file. The fully updated `application.properties` file should be like below.

``` text
spring.application.name=spring-ai-datasql
spring.ai.bedrock.aws.region=us-east-1
spring.ai.bedrock.aws.timeout=5m
spring.ai.bedrock.anthropic3.chat.enabled=true
spring.ai.bedrock.anthropic3.chat.options.max-tokens=4000
spring.ai.bedrock.anthropic3.chat.model=anthropic.claude-3-5-sonnet-20240620-v1:0

# config below AWS credential key, it also can be configure in Java environments or System environments
spring.ai.bedrock.aws.access-key=${AWS_ACCESS_KEY_ID}
spring.ai.bedrock.aws.secret-key=${AWS_SECRET_ACCESS_KEY}
```

Now, we can run the application and test the API.

#### Test Example 1
**Input:** 

```
What's the total prices of product sales ?
```

**Output:**

The response will be like below.

``` sql
  SELECT SUM(price) FROM product_sales;
```

**Postman Screenshot:**

{% image /assets/images/ai-ml/gen-sql-by-ai-test-1.png %}

The `AI` model can generate the SQL text base on user input message.

#### Test Example 2
**Input:** 

```
How many customers by our products? I only need unique customers.
```

**Output:**

``` sql
SELECT COUNT(DISTINCT customer_id) FROM product_sales
```

**Postman Screenshot:**

{% image /assets/images/ai-ml/gen-sql-by-ai-test-2.png %}

#### Test Example 3
**Input:**

```
I want to see the total sales prices for each product categories.
```

**Output:**

``` sql
SELECT product_category, SUM(price) AS total_sales FROM product_sales JOIN products ON product_sales.product_id = products.product_id GROUP BY product_category;
```

**Postman Screenshot:**

{% image /assets/images/ai-ml/gen-sql-by-ai-test-3.png %}

#### Test Example 4
**Input:** 

```
Please show me all sales data which contains price, sales date, customer name and product name and product categories
```

**Output:**

``` sql
SELECT ps.price, ps.sale_date, c.customer_name, p.product_name, p.product_category FROM product_sales ps JOIN products p ON ps.product_id = p.product_id JOIN customers c ON ps.customer_id = c.customer_id
```

**Postman Screenshot:**

{% image /assets/images/ai-ml/gen-sql-by-ai-test-4.png %}

#### Test Example 5
**Input:** 

```
Please show total sales prices of each product category on 2nd quarter this year
```

**Output:**

``` sql
SELECT p.product_category, SUM(ps.price) AS total_sales FROM product_sales ps JOIN products p ON ps.product_id = p.product_id WHERE YEAR(ps.sale_date) = YEAR(CURDATE()) AND QUARTER(ps.sale_date) = 2 GROUP BY p.product_category
```

**Postman Screenshot:**

{% image /assets/images/ai-ml/gen-sql-by-ai-test-5.png %}

As you can see the `AI` model can generate the SQL text base on user input message. Base on this function, we can download the data from mysql or display the chart base on data queried. It's good for business analyst to query the data by natural language.







