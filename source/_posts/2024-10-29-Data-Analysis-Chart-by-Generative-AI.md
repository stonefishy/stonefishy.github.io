---
title: Data Analysis Chart by Generative AI
date: 2024-10-29 14:18:48
tags: [AI, AWS, Python, SQL]
categories: AI/ML
---

In data analysis, we often need to create charts to visualize the data by using BI tools, such as `Tableau`, `Power BI`, `AWS Quicksight`, or `Qlik Sense`. These tools allow us to create interactive and visually appealing charts, which can help us to identify patterns and trends in the data.

## General Solution Architecture for Data Analysis BI Chart
The general data analysis BI chart solution architecture like below:

{% image /assets/images/ai-ml/bi-chart-general-arch.png, alt="General Solution Architecture for Data Analysis BI Chart", width="800px" %}.

Usually, the engieering create business chart by using BI tools after data is ETL proceseed. If the business want to see the data distribution chart, they need to ask the data engineer to create the chart. The data engineer will create the chart using BI tools and share it with the business. This may take some time and effort.

## Solution Architecture for Data Analysis BI Chart by Generative AI
Think about the scenario where the business want to see the data distribution chart without the data engineer's help. How can we create the chart without the data engineer's help?

One way to create the data distribution chart without the data engineer's help is to use a generative AI model. The business just need to describe what the data they want to see and want to display as which chart type. The generative AI application will create the chart for them.

The core important thing we need to let the GenAI to understand user's natural language and generate the information which application can be use. The solution architecture like below:

{% image /assets/images/ai-ml/bi-chart-AI-arch.png, alt="AI Solution Architecture for Data Analysis BI Chart", width="800px" %}.

The AI application will take the user's natural language as input and generate the chart for them. The AI application will use the following steps to generate the chart:
1. Understand the user's natural language and generate the chart title, chart type and chart related sql.
2. Connect to the database or dataset and execute the chart related sql to get the data.
3. Use the data to create the chart using the chart type.

The AI model could be `ChatGPT`, `OpenAI` or `Claude` model. The AI model will generate the chart related sql, chart type and chart title based on the user's natural language. The AI model will use the chart type to create the chart.

For example, if the user's natural language is "Show the distribution of the sales by product category", the AI application will generate the chart title as "Sales Distribution by Product Category" and chart type as "Bar Chart". The AI application will execute the following sql to get the data:

```sql
SELECT product_category, SUM(sales) as total_sales
FROM sales_data
GROUP BY product_category
```

Below is a demo to generate the chart for the user's natural language base on testing sample data.

{% image /assets/images/ai-ml/bi-chart-by-ai-test-1.png, width="800px" %}.

User input natural language: 

``` text
"I want to see the distribution of all product categories with duplicate devices removed, and exclude the empty category, please display it in a pie chart."
```

{% image /assets/images/ai-ml/bi-chart-by-ai-test-1a.png, width="800px" %}.

The AI model response below base on the user's natural language and prompts.

``` json
{
    'sql': "SELECT category, COUNT(DISTINCT macaddress) as device_count FROM device_demo_data WHERE category != '' GROUP BY category ORDER BY device_count DESC", 
    'chart': 'pie', 
    'title': 'Distribution of Unique Devices by Product Category'
}
```

There is a question, how the AI model know to generate this data format for us? Actually it is because we provide the prompts to the AI model. The prompts will guide the AI model to generate the chart related sql, chart type and chart title. Below is sample prompts:

``` txt
You are a data analyst. Below is the table structure information about devices reported data. I will ask you questions, and then please generate the json data format {'sql':'','chart':'table','title':''} based on the questions I asked. 
Emphasize: I only need this json data format. The 'sql' value is used by AWS Athena to query and generate the chart data. 
The 'chart' value is the chart type, 'numeric' represents a just a number, 'table' represents a table chart, 'pie' represents a pie chart, 'bar' represents a bar chart, 'line' represents a line chart. 
The 'title' value is the chart title. Please remember, I only need the json string format data, don't need other sentence.
CREATE EXTERNAL TABLE `device_demo_data`(
  `macaddress` string COMMENT '设备mac地址 / The device macaddress', 
  `productname` string COMMENT '设备产品名称 / The device product name', 
  `category` string COMMENT '设备产品的分类 / The device product category', 
  `country` string COMMENT '设备所在的国家 / The country where the device is located', 
  `region` string COMMENT '设备上传数据所在的区域 / The region where the device data is reported', 
  `default_region` string COMMENT '设备出厂设置的默认区域 / The default region set by the device', 
  `oneosversion` string COMMENT '设备OneOS的版本号 / The OneOS version of the device', 
  `firmwareversion` string COMMENT '设备的固件版本号 / The firmware version of the device', 
  `officialversion` string COMMENT '设备是否是官方的发布版本，1为官方版本， 0为非官方版本 / Whether the device is an official release, 1 for official version, 0 for non-official version', 
  `createtime` string COMMENT '设备上报数据的日期, 数据类型是字符串，日期格式是2024-09-01，表示2024年9月1日 / The date when the device reported data, the data type is string, the date format is 2024-09-01, which means September 1, 2024'
)
```

We tell AI model the data schema and each field means, and indicate only need the json format response with specific field. Once the AI model generate the response, we can use it to create the chart for the user's natural language.

We can also generate the line chart base on time line.

User input natural language: 

```
How many distinct devices reported every week, exclude the empty date, display the data in line graph
```

{% image /assets/images/ai-ml/bi-chart-by-ai-test-2.png, width="800px" %}.

For the front-end UI to display the chart, we can use some chart library like `Echarts`, `Hightcharts`, `D3.js` or `Chart.js`. The front-end UI display the chart base on chart type and the data which queried by SQL.

All these generated charts can be added into dashboard.

{% image /assets/images/ai-ml/bi-chart-by-ai-dashboard.png, width="800px" %}.

## Limitation
As you can see, the AI solution architecture is a new way to create BI chart. However, it still has some limitations. It can not generate the complex chart which like some BI tools advanced chart fucntionality. But it is still a good start to create the chart for business users.






