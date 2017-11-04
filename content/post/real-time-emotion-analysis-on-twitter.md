+++
date = "2013-10-20T12:00:00"
draft = false
tags = ["big data", "semantic analysis"]
title = "Real-time Emotion Analysis On Twitter"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}


This post is about my project of Big Data course.

### Basic Concept
---

We use real-time big data analysis technologies to evaluate tweets about some brands and products. We will judge the emotion of tweets and decide if the tweet expresses positive, negative or no emotion about a specific brand or product. The company or merchant can observe the trend of customers’ emotion towards its brand or products, understand customers’ online behavior or re- action, based on which they can make better decisions and provide better services. This project will help companies find out what their customers feel and adjust their marketing strategies timely.

### Smarter Planet Information
---

When a company release its new products, the managers of this company must want to know what are people’s opinions about their products, or how their customers feel when they use their products. In this way, they can get people’s online reaction quickly and make better decisions onwards. Nowadays, thanks to the development of social network, it becomes true.

When customers use a product, they probably have some feelings, such as happy or hate. In old days, they could just communicate with people around them. However, today, they can post their feelings on social network, and instantly his fiends around the world can see it. Then maybe some of his friends will re-post it and more people will see it. The social network changes the way information spread and lead us to a new era of information.

Twitter is one of the most popular social networks. People tweet their emotion, feelings, status and locations and share interesting things with their friends.
However, sometimes these data are very large and hard to analyze. Our project is to use big data analytic technologies to evaluate these massive amount of data and extract the most useful in- formation, feelings. In addition, we plan to make it work in real-time so that the company can see the marketing reaction instantly. It can also help companies to provide better customer services in the future. For the investors, they can use these information to help them make decisions on investment. What is more, people can make stock price prediction and decide whether to buy their stocks or not.

Our project is in align with the topic “Smarter Commerce” in IBM smarter planet. It can help companies to optimize their marketing processes by tracking online behavior to inform market- ing decisions, and capturing data from customers interactions for real time marketing. It provides a way for them to do better business.

### Resources
---

The following technologies may subject to change during the process of development. These are some technologies that may be used in our project, but some of them still need more comparisons and to be decided then.

Hadoop[1]: Hadoop is an open-source software framework that supports data-intensive distributed applications, and also supports running applications on large clusters of commodity hardware.

Impala[2]: Cloudera Impala is an open source project created by Cloudera that provides SQL query execution to enable fast interactive ad-hoc queries, on data stored in Hadoop.

Storm[3]: Storm is a free and open source distributed realtime computation system. It has many use cases: realtime analytics, online machine learning, continuous computation, distributed RPC, ETL, and more.

Hive[4]: Hive is a data warehouse infrastructure built on top of Hadoop for providing data summarization, query, and analysis. – Semantic Analytics: We need to use semantic analytics to extract the emotion information from massive amount of tweets related to a specific brands or products. We still need a real-time visualizing tool to present the output.

Flume[5]: Flume is a distributed, reliable, and available service for efficiently collecting, aggregating, and moving large amounts of log data.

Others: As all of our group members are familiar with Java, so we decided to use Java as our main programing language. But we may still use some other script language, such as Bash Script, Python, etc, to complete some simple tasks.

The architecture of the whole system may be presented like this below. We are going to detail it in the future and make it better.

### Archtecture
---

![](/img/blog/twitter-emotion.png)

### Risks
---

We think a lot about the problems we may encounter during the development. Here is a short list of some risks and how we prepare to face them.

Real-time processing: This is the first time for all the our group members to use real-time processing, especially with Hadoop and related tools. It is very new but some researches have been done in these years. Some open-source products are released like Storm and Impala, which will help us a lot. We need to try our best to learn how to build real-time processing system and figure out how to apply it on Hadoop in order to take advantage of the MapReduce computing model and Hadoop File System.

This risk also includes the flow balance system. How to deal with many real-time requests and how to balance the usage of each node is what we will figure out.

Semantic Analytics: This is another challenge for us because, like real-time processing, this is also the first time we use it. So we are woking on some papers in this field these days and try to apply it to analyze the emotion status towards a brand or product in tweets. This is one of the most important parts of the whole system, and we need to let it run on multiple node using Ma- pReduce.

### References
---

[1] “Hadoop” http://hadoop.apache.org/

[2] “Impala” http://www.cloudera.com/content/cloudera/en/products/cdh/impala.html

[3] “Storm” http://storm-project.net/

[4] “Hive” http://hive.apache.org/

[5] “Flume” http://flume.apache.org/
