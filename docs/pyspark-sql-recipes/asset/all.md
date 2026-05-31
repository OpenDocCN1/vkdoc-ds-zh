![](images/978-1-4842-4335-0_CoverFigure.jpg)

ISBN 978-1-4842-4334-3e-ISBN 978-1-4842-4335-0 [https://doi.org/10.1007/978-1-4842-4335-0](https://doi.org/10.1007/978-1-4842-4335-0) Library of Congress Control Number: 2019934769 © Raju Kumar Mishra and Sundar Rajan Raman 2019 Apress Standard Trademarked names, logos, and images may appear in this book. Rather than use a trademark symbol with every occurrence of a trademarked name, logo, or image we use the names, logos, and images only in an editorial fashion and to the benefit of the trademark owner, with no intention of infringement of the trademark. The use in this publication of trade names, trademarks, service marks, and similar terms, even if they are not identified as such, is not to be taken as an expression of opinion as to whether or not they are subject to proprietary rights. While the advice and information in this book are believed to be true and accurate at the date of publication, neither the authors nor the editors nor the publisher can accept any legal responsibility for any errors or omissions that may be made. The publisher makes no warranty, express or implied, with respect to the material contained herein. Distributed to the book trade worldwide by Springer Science+Business Media New York, 233 Spring Street, 6th Floor, New York, NY 10013\. Phone 1-800-SPRINGER, fax (201) 348-4505, e-mail orders-ny@springer-sbm.com, or visit www.springeronline.com. Apress Media, LLC is a California LLC and the sole member (owner) is Springer Science + Business Media Finance Inc (SSBM Finance Inc). SSBM Finance Inc is a Delaware corporation.

*To the Almighty, who guides me in every aspect of my life. And to my mother, Smt. Savitri Mishra, and my lovely wife, Smt. Smita Rani Pathak.*

Introduction

This book will take you on an interesting journey to learn about PySparkSQL and Big Data using a problem-solution approach. Every problem is followed by a detailed, step-by-step answer, which will improve your thought process for solving Big Data problems with PySparkSQL. The following is a brief description of each chapter:

*   Chapter [1](#469054_1_En_1_Chapter.xhtml) , “Introduction to PySparkSQL,” covers Many Big Data processing tools such as Apache Hadoop, Apache Pig, Apache Hive, and Apache Spark. The shortcomings of Hadoop and the evolution of Spark are discussed. It discusses PySparkSQL, includes an introduction to DataFrame, and covers structured streaming. A discussion of Apache Kafka is also included. This chapter also sheds light on some NoSQL databases like MongoDB and Cassandra.

*   Chapter [2](#469054_1_En_2_Chapter.xhtml) , “Installation,” will take you to the real battleground. You’ll learn how to install many Big Data processing tools such as Hadoop, Hive, Spark, MongoDB, and Apache Cassandra.

*   Chapter [3](#469054_1_En_3_Chapter.xhtml) , “IO in PySparkSQL,” will take you through many recipes that read data from many data sources using PySparkSQL. You’ll read data from many file formats like CSV, JSON, ORC, and Parquet, then from many RDBMS like MySQL and PostgreSQL. It also discusses how to read data from NoSQL databases like MongoDB and Cassandra using PySparkSQL. Then you see how to save the data into many sinks like files and RDBMS or NoSQL databases.

*   Chapter [4](#469054_1_En_4_Chapter.xhtml) , “Operations on PySparkSQL DataFrames,” explains different operations like data filtering, data transformation, and data sorting on DataFrames.

*   Chapter [5](#469054_1_En_5_Chapter.xhtml) , “Data Merging and Data Aggregation Using PySparkSQL,” shows how to perform data aggregation and data merging on DataFrames.

*   Chapter [6](#469054_1_En_6_Chapter.xhtml) , “SQL, NoSQL, and PySparkSQL,” shows how to perform SQL operations on DataFrames. It contains multiple recipes that will help you convert DataFrames to table-like structures and then apply SQL queries to them.

*   Chapter [7](#469054_1_En_7_Chapter.xhtml) , “Optimizing PySparkSQL,” shows you how to perform optimal joins that run faster. You will understand the basics of how Spark works in the background and, based on that, you will see multiple recipes that will help you optimize your SQL queries on DataFrames.

*   Chapter [8](#469054_1_En_8_Chapter.xhtml) , “Structured Streaming,” shows you how to use Spark streaming with streaming data. This chapter provides multiple recipes to help you apply Spark’s structured streaming APIs and SQLs to streaming data.

*   Chapter [9](#469054_1_En_9_Chapter.xhtml) , “GraphFrames,” shows you how to perform Graph operations on DataFrames. There are multiple GraphFrame recipes, including PageRank, that will help you to appreciate and apply complex graph operations using Spark’s GraphFrame.

Acknowledgments

My heartiest thanks to the Almighty. I also would like to thank my mother, Smt. Savitri Mishra; my sisters, Mitan and Priya; my cousins, Suchitra and Chandni; and my maternal uncle, Shyam Bihari Pandey; for their support and encouragement. I am very grateful to my sweet and beautiful wife, Smt. Smita Rani Pathak, for her continuous encouragement and love while I was writing this book. I thank my brother-in-law, Mr. Prafull Chandra Pandey, for his encouragement to write this book. I am very thankful to my sisters-in-law, Rinky, Reena, Kshama, Charu, and Dhriti, for their encouragement as well. I am grateful to Anurag Pal Sehgal, Saurabh Gupta, Devendra Mani Tripathi, Avinash Dash, Rajesh Thakur, and all my friends. My nephews, Rashu and Rishu. Last but not least, thanks to coordinating editor Aditee Mirashi and acquisitions editor Celestin Suresh John at Apress; without them, this book would not have been possible.

### About the Authors and About the Technical Reviewer

### About the Authors

### About the Technical Reviewer

# 1. Introduction to PySpark SQL

The amount of data that’s generated increases every day. Technology advances have facilitated the storage of huge amounts of data. This data deluge has forced users to adopt distributed systems. Distributed systems look for distributed programming, which requires extra care for fault tolerance and efficient algorithms. Distributed systems always look for two things—reliability of the system and availability of all the components.

Apache Hadoop ensures efficient computation and fault tolerance for distributed systems. Mainly it concentrates on reliability and availability. Apache Hadoop is easy to program, therefore many became interested in Big Data. E-commerce companies wanted to know more about their customers; the healthcare industry wanted to know insights from their data; and other industries were interested in knowing more about the data they were capturing. More data metrics were defined and more data points were collected.

Many open source Big Data tools emerged, including Apache Tez and Apache Storm . Many NoSQL databases also emerged to deal with huge data inflow. Apache Spark also evolved as a distributed system and became very popular over time.

This chapter discusses Big Data and Hadoop as a distributed system to process Big Data. We will also cover the Hadoop ecosystem frameworks, like Apache Hive and Apache Pig. We also throw light on the shortcomings of Hadoop and will discuss the development of Apache Spark. Then we will cover Apache Spark. We also discuss the different cluster managers that work with Apache Spark. We discuss PySpark SQL, which is the core of this book and its usefulness. Without discussing NoSQL, this chapter is not justified. So a discussion of the NoSQL databases, MongoDB and Cassandra, is included in this chapter. Sometimes we read data from Relational Database Management Systems (RDBMSs) as well, so this chapter discusses PostgreSQL.

## Introduction to Big Data

Big Data is one of the hottest topics of this era. But what is this Big Data? It describes a dataset that’s huge and is increasing with amazing speed. Apart from its volume and the velocity, Big Data is also characterized by the variety of data and its veracity. Let’s discuss volume, velocity, variety, and veracity in detail. These are also known as the 4V characteristics of Big Data.

### Volume

Data *volume* specifies the amount of data to be processed. For large amounts of data, we need large machines or distributed systems. The time of computation increases with the volume of data. So it’s better to use a distributed system if we can parallelize our computation. The volume might be of structured data, unstructured data, or something in between. If we have unstructured data, then the situation becomes more complex and computatively intensive. You might wonder, how big is Big Data? This is a debatable question. But in a general way, we can say that the amount of data that we can’t handle using a conventional system is defined as Big Data. Now let’s discuss the velocity of data.

### Velocity

Organizations are becoming more and more data conscious. A large amount of data is collected every moment. This means that the *velocity* of the data is increasing. How can a single system handle this velocity? The problem becomes complex when you have to analyze large inflows of data in real time. Many systems are being developed to deal with this huge inflow of data. Another component that differentiates conventional data from Big Data is the variety of data, discussed next.

### Variety

The variety of data can make it so complex that conventional data analysis systems cannot analyze it properly. What kind of variety are we talking about? Isn’t data just data? Image data is different than tabular data, because of the way it is organized and saved. Infinite numbers of filesystems are available. Every filesystem requires a different way of dealing with it. Reading and writing a JSON file is different than the way we deal with a CSV file. Nowadays, a data scientist has to deal with combinations of datatypes. The data you are going to deal with might be a combination of pictures, videos, text, etc. The variety of Big Data makes it more complex to analyze.

### Veracity

Can you imagine a logically incorrect computer program producing correct output? Similarly, data that’s not accurate is going to provide misleading results. Veracity, or data correctness, is an important concern. With Big Data, we have to think of the abnormality of the data. See Figure [1-1](#469054_1_En_1_Chapter.xhtml#Fig1).

![../images/469054_1_En_1_Chapter/469054_1_En_1_Fig1_HTML.png](images/469054_1_En_1_Chapter/469054_1_En_1_Fig1_HTML.png)

Figure 1-1

Big Data

## Introduction to Hadoop

Hadoop is a distributed and scalable framework that solves Big Data problems. Hadoop was developed by Doug Cutting and Mark Cafarella. Hadoop has been written in Java. It can be installed on a cluster of commodity hardware and it scales horizontally on distributed systems. Hadoop development was inspired by two research papers from Google:

*   “MapReduce: Simplified Data Processing on Large Clusters,” by Jeffrey Dean and Sanjay Ghemawat

*   “The Google File System,” by Sanjay Ghemawat, Howard Gobioff, and Shun-Tak Leung

Working on commodity hardware makes it very cost effective. If we are working on commodity hardware, faults are an inevitable issue. But Hadoop provides a fault-tolerant system for data storage and computation. This fault-tolerant capability made Hadoop very popular.

Hadoop has two components (see Figure [1-2](#469054_1_En_1_Chapter.xhtml#Fig2)). The first component, the Hadoop Distributed File System (HDFS), is a distributed filesystem. The second component is MapReduce. HDFS is for distributed data storage and MapReduce is to perform computations on the data stored in HDFS.

![../images/469054_1_En_1_Chapter/469054_1_En_1_Fig2_HTML.png](images/469054_1_En_1_Chapter/469054_1_En_1_Fig2_HTML.png)

Figure 1-2

Hadoop components

## Introduction to HDFS

HDFS is used to store large amounts of data in a distributed and fault-tolerant fashion. HDFS is written in Java and runs on commodity hardware. It was inspired by the Google research paper of the Google File System (GFS). It is a write once and read many times system, and it’s effective for large amounts of data.

HDFS has two components—NameNode and DataNode. These two components are Java daemon processes. A NameNode, which maintains meta data of files distributed on a cluster, work as masters for many DataNodes. HDFS divides a large file into small blocks and saves the blocks on different DataNodes. Actual file data blocks reside on DataNodes.

HDFS provides a set of UNIX-shell like commands. But we can use the Java filesystem API provided by HDFS to work at a finer level on large files. Fault tolerance is implemented using replication of data blocks.

We can access the HDFS files using single thread processes in parallel. HDFS provides a very useful utility, called `distcp`, which is generally used to transfer data in a parallel fashion from one HDFS system to another. It copies data using parallel map tasks. We can see the HDFS components in the simple diagram in Figure [1-3](#469054_1_En_1_Chapter.xhtml#Fig3).

![../images/469054_1_En_1_Chapter/469054_1_En_1_Fig3_HTML.png](images/469054_1_En_1_Chapter/469054_1_En_1_Fig3_HTML.png)

Figure 1-3

Components of HDFS

## Introduction to MapReduce

The MapReduce model of computation first appeared in a research paper by Google. This research paper was implemented in Hadoop as Hadoop’s MapReduce. Hadoop’s MapReduce is a computation engine of the Hadoop Framework, which does computations on distributed data in HDFS. MapReduce has been found horizontally scalable on distributed systems of commodity hardware. It also scales up for large problems. In MapReduce, the problem solution is broken into two phases—the Map phase and the Reduce phase. In the Map phase, chunks of data are processed and in the Reduce phase, aggregation or reduction operations run on the results from the Map phase. Hadoop’s MapReduce framework was written in Java.

MapReduce is a master-slave model. In Hadoop 1, this MapReduce computation was managed by two daemon processes—Jobtracker and Tasktracker. Jobtracker is master process that deals with many Tasktrackers. Tasktracker is a slave to Jobtracker. But in Hadoop 2, Jobtracker and Tasktracker were replaced by YARN.

Hadoop’s MapReduce framework was written in Java. We can write our MapReduce code using the API provided by the framework and in Java. The Hadoop streaming module enables programmers with Python and Ruby knowledge to program MapReduce.

MapReduce algorithms have many uses. Many machine learning algorithms were implemented as Apache Mahout, which runs on Hadoop as Pig and Hive.

But MapReduce is not very good for iterative algorithms. At the end of every Hadoop job, MapReduce saves the data to HDFS and reads it back again for the next job. We know that reading and writing data to files are costly activities. Apache Spark mitigated the shortcomings of MapReduce by providing in-memory data persistence and computation.

### Note

You can read more about MapReduce and Mahout on the following web pages

[`https://www.usenix.org/legacy/publications/library/proceedings/osdi04/tech/full_papers/dean/dean_html/index.html`](https://www.usenix.org/legacy/publications/library/proceedings/osdi04/tech/full_papers/dean/dean_html/index.html)

[`https://mahout.apache.org/users/basics/quickstart.html`](https://mahout.apache.org/users/basics/quickstart.html)

## Introduction to Apache Hive

Computer science is a world of abstraction. Everyone knows that data is information in the form of bits. Programming languages like C provide abstraction over the machine and assembly language. More abstraction is provided by other high-level languages. Structured Query Language (SQL) is one of those abstractions. SQL is used all over the world by many data modeling experts. Hadoop is good for Big Data analysis. So how can a large population, knowing SQL, utilize the power of Hadoop computational power on Big Data? In order to write Hadoop's MapReduce program, users must know a programming language that can be used to program Hadoop’s MapReduce.

Real world day-to-day problems follow certain patterns. Some problems are common in day-to-day life, such as manipulation of data, handling missing values, data transformation, and data summarization. Writing MapReduce code for these day-to-day problems is great head-spinning work for non-programmers. Writing code to solve a problem is not a very intelligent thing. But writing efficient code that has performance scalability and can be extended is valuable. With this problem in mind, Apache Hive was developed at Facebook, so that day-to-day problems could be solved without having to write MapReduce code for general problems.

According to the language of the Hive wiki, “Hive is a data warehousing infrastructure based on Apache Hadoop”. Hive has its own SQL dialect, known as the Hive Query Language. It is known as HiveQL or sometimes HQL. Using HiveQL, Hive queries data in HDFS. Hive not only runs on HDFS but it runs on Spark too and other Big Data frameworks like Apache Tez.

Hive provides a Relational Database Management System-like abstraction to users for structured data in HDFS. You can create tables and run SQL-like queries on them. Hive saves the table schema in some RDBMS. Apache Derby is the default RDBMS that ships with the Apache Hive distribution. Apache Derby has been fully written in Java and is an open source RDBMS that comes with Apache License Version 2.0.

HiveQL commands are transformed into Hadoop's MapReduce code, and then they run on the Hadoop cluster. You can see the Hive command execution flow in Figure [1-4](#469054_1_En_1_Chapter.xhtml#Fig4).

![../images/469054_1_En_1_Chapter/469054_1_En_1_Fig4_HTML.png](images/469054_1_En_1_Chapter/469054_1_En_1_Fig4_HTML.png)

Figure 1-4

Code execution flow in Apache Hive

A person knowing SQL can easily learn Apache Hive and HiveQL and can use the storage and computation power of Hadoop in their day-to-day data analysis jobs on Big Data. HiveQL is also supported by PySpark SQL. You can run HiveQL commands in PySpark SQL. Apart from executing HiveQL queries, you can also read data from Hive directly to PySpark SQL and write results to Hive. See Figure [1-4](#469054_1_En_1_Chapter.xhtml#Fig4).

### Note

You can read more about Hive and Apache Derby RDBMS from the following web pages

[`https://cwiki.apache.org/confluence/display/Hive/Tutorial`](https://cwiki.apache.org/confluence/display/Hive/Tutorial)

[`https://db.apache.org/derby/`](https://db.apache.org/derby/)

## Introduction to Apache Pig

Apache Pig is data flow framework for performing data analysis on a huge amount of data. It was developed by Yahoo!, and it was open sourced to Apache Software Foundation. It’s now available under Apache License Version 2.0\. The Pig programming language is a Pig Latin scripting language. Pig is loosely connected to Hadoop, which means that we can connect it to Hadoop and perform many analysis. But Pig can be used with other tools like Apache Tez and Apache Spark.

Apache Hive is used as a reporting tool where Apache Pig is used to extract, transform, and load (ETL). We can extend the functionality of Pig using user-defined functions (UDF). User-defined functions can be written in many languages, including Java, Python, Ruby, JavaScript, Groovy, and Jython.

Apache Pig uses HDFS to read and store the data and Hadoop’s MapReduce to execute the algorithms. Apache Pig is similar to Apache Hive in terms of using the Hadoop cluster. As Figure [1-5](#469054_1_En_1_Chapter.xhtml#Fig5) depicts, on Hadoop, Pig Latin commands are first transformed to Hadoop’s MapReduce code. They are then transformed into MapReduce code, which runs on the Hadoop cluster.

![../images/469054_1_En_1_Chapter/469054_1_En_1_Fig5_HTML.png](images/469054_1_En_1_Chapter/469054_1_En_1_Fig5_HTML.png)

Figure 1-5

Code execution flow in Apache Pig

The best part of Pig is that the code is optimized and tested to work with day-to-day problems. So users can directly install Pig and start using it. Pig provides the Grunt shell to run interactive Pig commands. So anyone who knows Pig Latin can enjoy the benefits of HDFS and MapReduce, without knowing advanced programming languages like Java or Python.

### Note

You can read more about Apache Pig on

[`http://pig.apache.org/docs/`](http://pig.apache.org/docs/)

[`https://en.wikipedia.org/wiki/Pig_(programming_tool)`](https://en.wikipedia.org/wiki/Pig_%2528programming_tool%2529)

[`https://cwiki.apache.org/confluence/display/PIG/Index`](https://cwiki.apache.org/confluence/display/PIG/Index)

## Introduction to Apache Kafka

Apache Kafka is a publish-subscribe, distributed messaging platform. It was developed at LinkedIn and further open sourced to the Apache foundation. It is fault tolerant, scalable, and fast. Messages in Kafka terms, which is the smallest unit of data, flow from producer to consumer through the Kafka Server, and can be persisted and used at a later time. You might be confused about the terms *producer* and *consumer*. We are going to discuss these terms very soon. Another key term we are going to use in the context of Kafka is *topic*. A topic is a stream of messages of a similar category. Kafka comes with a built-in API that developers can use to build their applications. We are the ones who define the topic. Next we discuss the three main components of Apache Kafka.

### Producer

The Kafka producer produces the message to a Kafka topic. It can publish data to more than one topic.

### Broker

This is the main Kafka server that runs on a dedicated machine. Messages are pushed to the broker by the producer. The broker persists topics in different partitions and these partitions are replicated to different brokers to deal with faults. It is stateless in nature, therefore the consumer has to track the message it consumes.

### Consumer

The consumer fetches the message from the Kafka Broker. Remember, it fetches the messages. The Kafka Broker is not pushing messages to the consumer; rather, the consumer is pulling data from the Kafka Broker. Consumers are subscribed to one or more topics on the Kafka Broker and they read the messages. The broker also keeps tracks of all the messages it has consumed. Data is persisted in the broker for a specified time. If the consumer fails, it can fetch the data after it restarts.

Figure [1-6](#469054_1_En_1_Chapter.xhtml#Fig6) shows the message flow of Apache Kafka. The producer publishes messages to the topics. Then the consumer pulls data from the broker. In between the publishing and the pulling, the message is persisted by the Kafka Broker.

![../images/469054_1_En_1_Chapter/469054_1_En_1_Fig6_HTML.png](images/469054_1_En_1_Chapter/469054_1_En_1_Fig6_HTML.png)

Figure 1-6

Apache Kafka message flow

We will integrate Apache Kafka with PySpark in Chapter [7](#469054_1_En_7_Chapter.xhtml) and discuss Kafka in more detail.

### Note

You can read more about Apache Kafka at

[`https://kafka.apache.org/documentation/`](https://kafka.apache.org/documentation/)

[`https://kafka.apache.org/quickstart`](https://kafka.apache.org/quickstart)

## Introduction to Apache Spark

Apache Spark is a general purpose, distributed programming framework. It is considered very good for iterative as well as batch processing data. It was developed at the AMP lab. It provides an in-memory computation framework. It is open source software. On the one hand, it is best with batch processing, and on the other hand, it works very well with real-time or near real-time data. Machine learning and graph algorithms are iterative in nature and this is where Spark does its magic. According to its research paper, it is much faster than its peer, Hadoop. Data can be cached in memory. Caching intermediate data in iterative algorithms provides amazingly fast processing. Spark can be programmed using Java, Scala, Python, and R.

You can read more about Apache Spark runtime efficiency in the following research papers:

*   “Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing,” by Matei Zaharia, Mosharaf Chowdhury, Tathagata Das, Ankur Dave, Justin Ma, Murphy McCauley, Michael J. Franklin, Scott Shenker, and Ion Stoica.

*   “Spark: Cluster Computing with Working Sets,” by Matei Zaharia, Mosharaf Chowdhury, Michael J. Franklin, Scott Shenker, and Ion Stoica.

*   “MLlib: Machine Learning in Apache Spark,” by Xiangrui Meng, Joseph Bradley, Burak Yavuz, Evan Sparks, Shivaram Venkataraman, Davies Liu, Jeremy Freeman, DB Tsai, Manish Amde, Sean Owen, Doris Xin, Reynold Xin, Michael J. Franklin, Reza Zadeh, Matei Zaharia, and Ameet Talwalkar.

If you consider Spark as improved Hadoop, up to some extent, this is fine in my view. Because we can implement the MapReduce algorithm in Spark, Spark uses the benefit of HDFS. That means it can read data from HDFS and store data to HDFS, and it handles iterative computation efficiently because data can be persisted in memory. Apart from in-memory computation, it is good for interactive data analysis.

There are many other libraries that also sit on PySpark to make working with PySpark easier. We are going to discuss some as follows.

*   *MLlib*: MLlib is a wrapper over the PySpark core, and it deals with machine learning algorithms. The machine learning APIs provided by the MLlib library are very easy to use. MLlib supports many machine learning algorithms of classification, clustering, text analysis, and many more.

*   *ML*: ML is also a machine learning library that sits on the PySpark core. Machine learning APIs of ML can work on DataFrames.

*   *GraphFrames* : The GraphFrames library provides a set of APIs to do graph analysis efficiently using PySpark core and PySpark SQL. At the time of writing this book, DataFrames is an external library. You have to download and install it separately.

## PySpark SQL: An Introduction

Most data that data scientists deal with is either structured or semi-structured in nature. The PySpark SQL module is a higher level abstraction over that PySpark core in order to process structured and semi-structured datasets. We are going to study PySpark SQL throughout the book. It comes built into PySpark, which means that it does not require any extra installation.

PySpark SQL can be programmed using a domain-specific language. As in our case, we are going to use Python to program PySpark SQL. Apart from a domain-specific language, we can run Structured Query Language (SQL) and HiveQL queries. Using SQL and HiveQL makes PySpark SQL popular among database programmers and Apache Hive users.

Using PySpark SQL, you can read data from many sources. PySpark SQL supports reading from many file format systems, including text files, CSV, ORC, Parquet, JSON, etc. You can read data from Relational Database Management System (RDBMS), such as MySQL and PostgreSQL. You can save the analysis report to many systems and file formats too.

PySpark SQL introduced DataFrames, which create tabular representations of structured data and provide tables in RDBMS. Another data abstraction dataset was introduced in Spark 1.6, but it does not work with PySpark SQL.

### Introduction to DataFrames

DataFrames are an abstraction similar to tables in Relational Database Systems. They consist of named columns. DataFrames are collections of row objects, which are defined in PySpark SQL. DataFrames consist of named column objects as well. Users know the schema of the tabular form, so it becomes easy to operate on DataFrames.

Elements within columns of a DataFrame will have the same datatype. Rows in a DataFrame might consist of elements of different datatypes. The basic data structure is called a Resilient Distributed Dataset (RDD). DataFrames are wrappers over the RDD. They are RDD or row objects. We will read more about the `Row` class in coming chapters. See Figure [1-7](#469054_1_En_1_Chapter.xhtml#Fig7).

![../images/469054_1_En_1_Chapter/469054_1_En_1_Fig7_HTML.png](images/469054_1_En_1_Chapter/469054_1_En_1_Fig7_HTML.png)

Figure 1-7

DataFrames

Figure [1-7](#469054_1_En_1_Chapter.xhtml#Fig7) depicts a DataFrame with four columns. The name of the columns are `id`, `Gender`, `Occupation`, and `swimTimeInSecond`. The datatype of the first three columns is `String`, whereas the last column contains floating-point data. The DataFrame has two components—the data and its schema. It should be clear that this is similar to RDBMS tables or R programming DataFrames .

### Note

Datasets are not used in PySpark. The interested reader can read about datasets and their APIs at the following link

[`https://spark.apache.org/docs/latest/sql-programming-guide.html`](https://spark.apache.org/docs/latest/sql-programming-guide.html)

### SparkSession

A `SparkSession` object is the entry point that replaced `SQLContext` and `HiveContext`. To make PySpark SQL code compatible with previous versions, `SQLContext` and `HiveContext` continue in PySpark. In the PySpark console, we get the `SparkSession` object. We can create a `SparkSession` object using the following code.

In order to create a `SparkSession` object, we have to import SparkSession as follows.

```
from pyspark.sql import SparkSession
```

After importing SparkSession, we can create its object using `SparkSession.builder` as follows.

```
spark = SparkSession.builder.appName("PythonSQLAPP") .getOrCreate()
```

The `appName` function will set the name of the application. The `getOrCreate()` function will return an existing `SparkSession` object. If no `SparkSession` object exists, the `getOrCreate()` function will create a new object and return it.

### Structured Streaming

We can stream data analysis using the Structured Streaming framework, which is a wrapper over PySpark SQL. We can perform analysis on streaming data in a similar fashion using Structured Streaming as we perform batch analysis on static data using PySpark SQL.

Just as the Spark streaming module performs streaming operations on mini-batches, the Structured Streaming engine performs streaming operations on mini-batches.

The best part of structured streaming is that it uses a similar API as for PySpark SQL. Therefore, the learning curve is high. Operations on DataFrames are optimized and, in a similar fashion, the Structured Streaming API is optimized in the context of performance.

### Catalyst Optimizer

SQL is a declarative language. Using SQL, we tell the SQL engine what to do. We do not tell it how to perform the task. Similarly, PySpark SQL commands do not tell it how to perform a task. These commands only tell it what to perform. So, the PySpark SQL queries require optimization while performing tasks. The catalyst optimizer performs query optimization in PySpark SQL. The PySpark SQL query is transformed into low-level Resilient Distributed Dataset (RDD) operations. The catalyst optimizer first transforms the PySpark SQL query to a logical plan and then transforms this logical plan to an optimized logical plan. From this optimized logical plan, a physical plan is created. More than one physical plan is created. Using a cost analyzer, the most optimized physical plan is selected. And finally, low-level RDD operation code is created.

### Introduction to Cluster Managers

In a distributed system, a job or application is broken into different tasks, which can run in parallel on different machines in the cluster. If the machines fail, you have to reschedule the task on another machine.

The distributed system generally faces scalability problems due to mismanagement of resources. Consider a job that’s already running on a cluster. Another person wants to run another job. That second job has to wait until the first is finished. But in this way we are not utilizing the resources optimally. Resource management is easy to explain but difficult to implement on a distributed system. Cluster managers were developed to manage cluster resources optimally. There are three cluster managers available for Spark—standalone, Apache Mesos, and YARN. The best part of these cluster managers is that they provide an abstraction layer between users and clusters. The user experience is like working on a single machine, although they are working on a cluster, due to the abstraction provided by the cluster managers. Cluster managers schedule cluster resources to running applications.

#### Standalone Cluster Manager

Apache Spark ships with a standalone cluster manager. It provides a master-slave architecture to Spark clusters. It is a Spark-only cluster manager. You can run only Spark applications using this standalone cluster manager. Its components are the master and the workers. Workers are slaves to the master process. It is the simplest cluster manager. The Spark standalone cluster manager can be configured using scripts in the `sbin` directory of Spark. We will configure the Spark standalone cluster manager in coming chapters and will deploy PySpark applications using the standalone cluster manager.

#### Apache Mesos Cluster Manager

Apache Mesos is a general purpose cluster manager. It was developed at the University of California, Berkeley AMP lab. Apache Mesos helps distributed solutions to scale efficiently. You can run different applications using different frameworks on the same cluster using Mesos. What is the meaning of different applications from different frameworks? This means that you can run a Hadoop application and a Spark application simultaneously on Mesos. While multiple applications are running on Mesos, they share the resources of the cluster. Apache Mesos has two important components—the master and slaves. This master-slaves architecture is similar to the Spark Standalone cluster manager. The applications running on Mesos are known as frameworks. Slaves tell the master about the resources available to it as a resource offer. The slave machine provides resource offers periodically. The allocation module of the master server decides which framework gets the resources.

#### YARN Cluster Manager

YARN stands for “Yet Another Resource Negotiator”. YARN was introduced in Hadoop 2 to scale Hadoop. Resource management and job management were separated. Separating these two components made Hadoop scale better. YARN’s main components are the ResourceManager, ApplicationMaster, and NodeManager. There is one global ResourceManager and many NodeManagers will be running per cluster. NodeManagers are slaves to the ResourceManager. The Scheduler, which is a component of the ResourceManager, allocates resources for different applications working on clusters. The best part is that you can run a Spark application and any other applications like Hadoop or MPI simultaneously on clusters managed by YARN . There is one ApplicationMaster per application, and it deals with the task running in parallel on the distributed system. Remember, Hadoop and Spark have their own ApplicationMaster.

### Note

You can read more about standalone, Apache Mesos, and YARN cluster managers on the following web pages.

[`https://spark.apache.org/docs/2.0.0/spark-standalone.html`](https://spark.apache.org/docs/2.0.0/spark-standalone.html)

[`https://spark.apache.org/docs/2.0.0/running-on-mesos.html`](https://spark.apache.org/docs/2.0.0/running-on-mesos.html)

[`https://spark.apache.org/docs/2.0.0/running-on-yarn.html`](https://spark.apache.org/docs/2.0.0/running-on-yarn.html)

## Introduction to PostgreSQL

Relational Database Management Systems are still very common in many organizations. What is the meaning of *relation* here? Relation means tables. PostgreSQL is a Relational Database Management System. It runs on all major operating systems like Microsoft Windows, UNIX-based operating systems, MacOS X, and many more. It is an open source program and the code is available under the PostgreSQL license. Therefore, you can use it freely and modify it according to your requirements.

PostgreSQL databases can be connected through other programming languages like Java, Perl, Python, C, and C++, and many other languages through different programming interfaces. It can be also be programmed using a procedural programming language called PL/pgSQL (Procedural Language/PostgreSQL) , which is similar to PL/SQL. You can add custom functions to this database. You can write custom functions in C/C++ and other programming languages. You can also read data from PostgreSQL from PySpark SQL using JDBC connectors. In the coming chapters, we are going to read data tables from PostgreSQL using PySpark SQL. We are also going to explore some more facets of PostgreSQL in coming chapters.

PostgreSQL follows the ACID (Atomicity, Consistency, Isolation and Durability) principles. It comes with many features, some of which are unique to PostgreSQL. It supports updatable views, transactional integrity, complex queries, triggers, and others. PostgreSQL does its concurrency management using the multi-version concurrency control model.

There is wide community support for PostgreSQL. PostgreSQL has been designed and developed to be extensible.

### Note

If you want to learn about PostgreSQL in depth, the following links will be very helpful.

[`https://wiki.postgresql.org/wiki/Main_Page`](https://wiki.postgresql.org/wiki/Main_Page)

[`https://en.wikipedia.org/wiki/PostgreSQL`](https://en.wikipedia.org/wiki/PostgreSQL)

[`https://en.wikipedia.org/wiki/Multiversion_concurrency_control`](https://en.wikipedia.org/wiki/Multiversion_concurrency_control)

[`http://postgresguide.com/`](http://postgresguide.com/)

## Introduction to MongoDB

MongoDB is a document-based NoSQL database. It is an open source, distributed database, which was developed by MongoDB Inc. MongoDB is written in C++ and it scales horizontally. It is being used by many organizations for backend databases and for many other purposes.

MongoDB comes with a mongo shell, which is a JavaScript interface to the MongoDB server. The mongo shell can be used to run queries as well as perform administrative tasks. On the mongo shell, we can run JavaScript code too.

Using PySpark SQL, we can read data from MongoDB and perform analyses. We can also write the results.

### Note

You can read more about MongoDB from the following link

[`https://docs.mongodb.com/`](https://docs.mongodb.com/)

## Introduction to Cassandra

Cassandra is open source, distributed database that comes with the Apache License. It is a NoSQL database developed at Facebook. It is horizontally scalable and works best with structured data. It provides a high level of consistency and has tunable consistency. It does not have a single point of failure. It replicates data on different nodes with a peer-to-peer distributed architecture. Nodes exchange their information using the gossip protocol.

### Note

You can read more about Apache Cassandra from the following links

[`https://www.datastax.com/resources/tutorials`](https://www.datastax.com/resources/tutorials)

[`http://cassandra.apache.org/doc/latest/`](http://cassandra.apache.org/doc/latest/)

# 2. Installation

In the upcoming chapters, we are going to solve many problems using PySpark. PySpark also interacts with many other Big Data frameworks to provide end-to-end solutions. PySpark might read data from HDFS, NoSQL databases, or a relational database management system. After data analysis, we can save the results into HDFS or databases.

This chapter deals with all the software installations that are required to go through this book. We are going to install all the required Big Data frameworks on the CentOS operating system. CentOS is an enterprise-class operating system. It is free to use and easily available. We can download CentOS from the [`https://www.centos.org/download/`](https://www.centos.org/download/) link and install it on a virtual machine.

In this chapter, we are going to discuss the following recipes:

*   Recipe 2-1\. Install Hadoop on a single machine

*   Recipe 2-2\. Install Spark on a single machine

*   Recipe 2-3\. Use the PySpark shell

*   Recipe 2-4\. Install Hive on a single machine

*   Recipe 2-5\. Install PostgreSQL

*   Recipe 2-6\. Configure the Hive metastore on PostgreSQL

*   Recipe 2-7\. Connect PySpark to Hive

*   Recipe 2-8\. Install MySQL

*   Recipe 2-9\. Install MongoDB

*   Recipe 2-10\. Install Cassandra

I suggest that you install every piece of software on your own. It is a good exercise and will give you a deeper understanding of the components of each software package.

## Recipe 2-1\. Install Hadoop on a Single Machine

### Problem

You want to install Hadoop on a single machine.

### Solution

You might be wondering, why are we installing Hadoop while we are learning PySpark? Are we going to use Hadoop MapReduce as the distributed framework for our problem solving? The answer is, not at all. We are going to use two components of Hadoop—HDFS and YARN. HDFS for data storage and YARN as a cluster manager. In order to install Hadoop, we need to download and configure it.

### How It Works

Follow these steps to complete the Hadoop installation.

#### Step 2-1-1\. Creating a New CentOS User

A new user is created. You might be thinking, why a new user? Why can’t we install Hadoop in an existing user? The reason behind that is that we want to provide a dedicated user for all the Big Data frameworks. In the following lines of code, we are going to create a user named `pysparksqlbook`.

```
[root@localhost book]# adduser pysparksqlbook
[root@localhost book]# passwd pysparksqlbook’
```

Here is the output:

```
Changing password for user pysparksqlbook.
New password:
passwd: all authentication tokens updated successfully.
```

In the above part of the code, we can see that the `adduser` command has been used to create or add a user. The Linux `passwd` command has been used to provide a password to our new user `pysparksqlbook`.

After creating a user, we have to add it to sudo. Sudo stands for “superuser do”. Using sudo, we can run any code as the superuser. Sudo will be used to install the software.

#### Step 2-1-2\. Adding a CentOS user to sudo

Then, we have to add our new user `pysparksqlbook` to the sudo. The following command will do this.

```
[root@localhost book]# usermod -aG wheel pysparksqlbook [root@localhost book]# exit
```

Then we enter our user `pysparksqlbook`.

```
[book@localhost ~]$ su pysparksqlbook
```

We will create two directories—the `binaries` directory under the home directory to download software and the `allBigData` directory under the root / directory to install the Big Data frameworks.

```
[pysparksqlbook@localhost ~]$ mkdir binaries [pysparksqlbook@localhost ~]$ sudo mkdir /allBigData
```

#### Step 2-1-3\. Installing Java

Hadoop, Hive, Spark, and many Big Data frameworks use JVM. It’s why we are first going to install Java. We are going to use OpenJDK for our purposes. We are installing the 8th version of OpenJDK. We can install Java on CentOS using the `yum` installer. The following command installs Java using the `yum` installer.

```
[pysparksqlbook@localhost binaries]$ sudo yum install java-1.8.0-openjdk
```

Here is the output:

```
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
.
.
.
.
Updated:
java-1.8.0-openjdk.x86_64 1:1.8.0.181-3.b13.el7_5
Dependency Updated:
java-1.8.0-openjdk-headless.x86_64 1:1.8.0.181-3.b13.el7_5
Complete!
```

Java has been installed. After installation of any software, it is a good idea to check the installation. Checking the installation will show you that everything is fine.

In order to check the Java installation, I prefer the `java -version` command, which will return the version of JVM installed.

```
[pysparksqlbook@localhost binaries]$ java -version
```

Here is the output:

```
openjdk version "1.8.0_181"
OpenJDK Runtime Environment (build 1.8.0_181-b13)
OpenJDK 64-Bit Server VM (build 25.181-b13, mixed mode)
```

Java has been installed. We have to look for the environment variable `JAVA_HOME`, which is going to be used by all the distributed frameworks. After installing Java, we can find the Java home variable by using `jrunscript`, as follows.

```
[pysparksqlbook@localhost  binaries]$jrunscript -e 'java.lang.System.out.println(java.lang.System.getProperty("java.home"));'
```

Here is the output:

```
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
```

We have found the absolute path of `JAVA_HOME` .

#### Step 2-1-4\. Creating Password-Less Logging from pysparksqlbook

Here are the steps for creating a password-less login.

```
[pysparksqlbook@localhost binaries]$ ssh-keygen -t rsa
```

Here is the output:

```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/pysparksqlbook/.ssh/id_rsa):
Created directory '/home/pysparksqlbook/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/pysparksqlbook/.ssh/id_rsa.
Your public key has been saved in /home/pysparksqlbook/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:DANT7QBm9fDHi1/VRPcytb8/d4PemcOnn0Sm9hzl93A pysparksqlbook@localhost.localdomain
The key's randomart image is:
+---[RSA 2048]----+
|    *++.       .=|
|   o o.+..     ++|
|      ooo o   +.o|
|       +.o . . o.|
|        S . .  oo|
|         . .  +.o|
|          .  o++E|
|            ..=B%|
|            ...X@|
+----[SHA256]-----+
```

The next command:

```
[pysparksqlbook@localhost binaries]$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
[pysparksqlbook@localhost binaries]$ chmod 755 ~/.ssh/authorized_keys
[pysparksqlbook@localhost binaries]$ ssh localhost
```

Here is the output:

```
The authenticity of host 'localhost (::1)' can't be established.
ECDSA key fingerprint is SHA256:md4M1J6VEYQm3gSynB0gqIYFpesp6I2cRvlEvJOIFFE.
ECDSA key fingerprint is MD5:78:cf:a7:71:2e:38:c2:62:01:65:c2:4c:71:7e:3c:90.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
Last login: Thu July 13 17:02:56 2018
```

Finally:

```
[pysparksqlbook@localhost ~]$ exit
```

Here is the output:

```
logout
Connection to localhost closed.
```

#### Step 2-1-5\. Downloading Hadoop

We are now going to download Hadoop from the Apache website. As mentioned, we will download all the required packages to the `binaries` directory. We are going to use the `wget` command to download Hadoop.

```
[pysparksqlbook@localhost ~]$ cd  binaries
[pysparksqlbook@localhost binaries]$ wget http://mirrors.fibergrid.in/apache/hadoop/common/hadoop-2.7.7/hadoop--.7.7.tar.gz
```

Here is the output:

```
--2018-06-26 12:56:36--  http://mirrors.fibergrid.in/apache/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz
Resolving mirrors.fibergrid.in (mirrors.fibergrid.in)... 103.116.36.9, 2402:f4c0::9
Connecting to mirrors.fibergrid.in (mirrors.fibergrid.in)|103.116.36.9|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 218720521 (209M) [application/x-gzip]
Saving to: 'hadoop-2.7.7.tar.gz'
100%[======================================>] 218,720,521 38.8KB/s   in 63m 45s
2018-06-26 14:00:22 (55.8 KB/s) - 'hadoop-2.7.7.tar.gz' saved [218720521/218720521]
```

#### Step 2-1-6\. Moving Hadoop Binaries to the Installation Directory

Our installation directory is called `allBigData` . The downloaded software is in `hadoop-2.7.7.tar.gz`, which is a compressed directory. So we first have to decompress it. We can decompress it using the `tar` command as follows:

```
[pysparksqlbook@localhost binaries]$ tar xvzf hadoop-2.7.7.tar.gz
```

Now we move Hadoop under the `allBigData` directory.

```
pysparksqlbook@localhost binaries]$ sudo  mv  hadoop-2.7.7   /allBigData/hadoop
```

#### Step 2-1-7\. Modifying the Hadoop Environment File

We have to make some changes to the Hadoop environment file. The Hadoop environment file is found in the Hadoop configuration directory. In our case, the Hadoop configuration directory is `/allBigData/hadoop/etc/hadoop/`. In the following line of code, we add `JAVA_HOME` to the `hadoop-env.sh` file.

```
[pysparksqlbook@localhost binaries]$ vim /allBigData/hadoop/etc/hadoop/hadoop-env.sh
```

After opening the Hadoop environment file, add the following line.

```
# The java implementation to use.
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
```

#### Step 2-1-8\. Modifying the Hadoop Properties Files

We will be focusing on three properties files:

*   `hdfs-site.xml`: HDFS properties

*   `core-site.xml`: Core properties related to the cluster

*   `mapred-site.xml`: Properties for the MapReduce Framework

These properties files will be found in the Hadoop configuration directory. In the previous chapter, we discussed HDFS. We found that HDFS has two components—`NameNode` and `DataNode`. We also discussed that HDFS does data replication for fault tolerance. In our `hdfs-site.xml` file, we are going to set the `namenode` directory using the `dfs.name``.dir` parameter, the `datanode` directory using the `dfs.data.dir` parameter, and the replication factor using the `dfs.replication` parameter.

Let’s modify `hdfs-site.xml`.

```
[pysparksqlbook@localhost binaries]$ vim /allBigData/hadoop/etc/hadoop/hdfs-site.xml
```

After opening `hdfs-site.xml` , we have to add the following lines to that file.

```
dfs.name.dir
file:/allBigData/hdfs/namenode
NameNode location

dfs.data.dir
file:/allBigData/hdfs/datanode
DataNode location

dfs.replication
1
 Number of block replication 

```

After updating `hdfs-site.xml`, we are going to update `core-site.xml`. In `core-site.xml`, we are going to update two properties—`fs.default.name` and `hadoop.tmp.dir`. The `fs.default.name` property is used to determine the host, port, etc. of the filesystem. The `hadoop.tmp.dir` property determines the temporary directories for Hadoop. We have to add the following lines to `core-site.xml`.

```
fs.default.name
hdfs://localhost:9745
Host port of file system

hadoop.tmp.dir
/application/hadoop/tmp
Temp directory for other working and tmp directories

```

Finally, we are going to modify `mapred-site.xml`. We are going to modify `mapreduce.framework.name`, which will decide which runtime framework is to be used. The possible values are local, classic, or yarn. We have to add the following code to the `mapred-site.xml` file.

```
[pysparksqlbook@localhost binaries]$ cp /allBigData/hadoop/etc/hadoop/mapred-site.xml.template /allBigData/hadoop/etc/hadoop/mapred-site.xml
[pysparksqlbook@localhost binaries]$vim /allBigData/hadoop/etc/hadoop/mapred-site.xml
```

Here is the XML:

```
mapreduce.framework.name
yarn

```

Let’s create the temporary directory:

```
[pysparksqlbook@localhost binaries]$ sudo mkdir -p  /application/hadoop/tmp
[pysparksqlbook@localhost binaries]$ sudo chown pysparksqlbook:pysparksqlbook -R /application/hadoop
```

#### Step 2-1-9\. Updating the .bashrc File

The following lines have to be added to the `.bashrc` file . Open `.bashrc` and append it by adding the following lines.

```
[pysparksqlbook@localhost binaries]$ vim  ~/.bashrc
```

In the `.bashrc` file, add the following line at the end.

```
export HADOOP_HOME=/allBigData/hadoop
export PATH=$PATH:$HADOOP_HOME/sbin
export PATH=$PATH:$HADOOP_HOME/bin
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
export PATH=$PATH:$JAVA_HOME/bin
```

Then we have to source the `.bashrc` file . After sourcing the file, new updated values will be reflected in the console.

```
[pysparksqlbook@localhost binaries]$ source ~/.bashrc
```

#### Step 2-1-10\. Running the Namenode Format

We have updated some property files. We are supposed to run `namenode` format, so that all the changes will be reflected in our framework. The following command will format `namenode`.

```
[pysparksqlbook@localhost binaries]$ hdfs namenode -format
```

Here is the output:

```
18/06/13 18:14:49 INFO namenode.FSImageFormatProtobuf: Image file /allBigData/hdfs/namenode/current/fsimage.ckpt_0000000000000000000 of size 331 bytes saved in 0 seconds.
18/06/13 18:14:50 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid >= 0
18/06/13 18:14:50 INFO util.ExitUtil: Exiting with status 0
18/06/13 18:14:50 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at localhost/127.0.0.1
************************************************************
```

#### Step 2-1-11\. Starting Hadoop

Hadoop has been installed. We have to start Hadoop now. We can find the Hadoop starting script in `/allBigData/hadoop/sbin/`. We have to run the `start-dfs.sh` and `start-yarn.sh` scripts , in sequence.

```
[pysparksqlbook@localhost binaries]$ /allBigData/hadoop/sbin/start-dfs.sh
```

Here is the output:

```
Starting namenodes on [localhost]
localhost: starting namenode, logging to /allBigData/hadoop/logs/hadoop-pysparksqlbook-namenode-localhost.localdomain.out
localhost: starting datanode, logging to /allBigData/hadoop/logs/hadoop-pysparksqlbook-datanode-localhost.localdomain.out
Starting secondary namenodes [0.0.0.0]
The authenticity of host '0.0.0.0 (0.0.0.0)' can't be established.
ECDSA key fingerprint is SHA256:md4M1J6VEYQm3gSynB0gqIYFpesp6I2cRvlEvJOIFFE.
ECDSA key fingerprint is MD5:78:cf:a7:71:2e:38:c2:62:01:65:c2:4c:71:7e:3c:90.
Are you sure you want to continue connecting (yes/no)? yes
0.0.0.0: Warning: Permanently added '0.0.0.0' (ECDSA) to the list of known hosts.
0.0.0.0: starting secondarynamenode, logging to /allBigData/hadoop/logs/hadoop-pysparksqlbook-secondarynamenode-localhost.localdomain.out
[pysparksqlbook@localhost binaries]$ /allBigData/hadoop/sbin/start-yarn.sh
```

Here is the output:

```
starting yarn daemons
starting resourcemanager, logging to /allBigData/hadoop/logs/yarn-pysparksqlbook-resourcemanager-localhost.localdomain.out
localhost: starting nodemanager, logging to /allBigData/hadoop/logs/yarn-pysparksqlbook-nodemanager-localhost.localdomain.out
```

#### Step 2-1-12\. Checking the Installation of Hadoop

We know that the `jps` command will show all the Java processes running on the machine. If everything is fine, it will show all the processes running as follows:

```
[pysparksqlbook@localhost binaries]$ jps
```

Here is the output:

```
13441 NodeManager
13250 ResourceManager
12054 DataNode
14054 Jps
12423 SecondaryNameNode
11898 NameNode
```

Congratulations to us. We have finally installed Hadoop on our system.

#### Step 2-1-13\. Stopping the Hadoop Processes

As we started Hadoop using the two scripts `start-dfs.sh` and `start-yarn.sh` in sequence, in a similar fashion, we can stop the Hadoop process using the `stop-dfs.sh` and `stop-yarn.sh` shell scripts , in sequence.

```
[pysparksqlbook@localhost binaries]$/allBigData/hadoop/sbin/stop-dfs.sh
```

Here is the output:

```
Stopping namenodes on [localhost]
localhost: stopping namenode
localhost: stopping datanode
Stopping secondary namenodes [0.0.0.0]
0.0.0.0: stopping secondarynamenode
[pysparksqlbook@localhost binaries]$/allBigData/hadoop/sbin/stop-yarn.sh
```

Here is the output :

```
stopping yarn daemons
stopping resourcemanager
localhost: stopping nodemanager
```

## Recipe 2-2\. Install Spark on a Single Machine

### Problem

You want to install Spark on a single machine.

### Solution

We are going to install prebuilt spark-2.3.0 for Hadoop version 2.7. We could build Spark from the source code. But we are going to use the prebuilt Apache Spark.

### How It Works

Follow these steps to complete the installation.

#### Step 2-2-1\. Downloading Apache Spark

We are going to download Spark from its mirror. We are going to use the `wget` command for that purpose, as follows.

```
[pysparksqlbook@localhost binaries]$ wget https://archive.apache.org/dist/spark/spark-2.3.0/spark-2.3.0-bin-hadoop2.7.tgz
```

Here is the output:

```
--2018-06-26 12:48:38--  https://archive.apache.org/dist/spark/spark-2.3.0/spark-2.3.0-bin-hadoop2.7.tgz
Resolving archive.apache.org (archive.apache.org)... 163.172.17.199
Connecting to archive.apache.org (archive.apache.org)|163.172.17.199|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 226128401 (216M) [application/x-gzip]
Saving to: 'spark-2.3.0-bin-hadoop2.7.tgz'
100%[======================================>] 226,128,401  392KB/s   in 5m 34s
2018-06-26 12:54:13 (662 KB/s) - 'spark-2.3.0-bin-hadoop2.7.tgz' saved [226128401/226128401]
```

#### Step 2-2-2\. Extracting the .tgz File of Spark

The following command will extract the `.tgz` file .

```
[pysparksqlbook@localhost binaries]$ tar xvzf spark-2.3.0-bin-hadoop2.7.tgz
```

#### Step 2-2-3\. Moving the Extracted Spark Directory to /allBigData

Now we have to move the extracted Spark directory to the `/allBigData` location. The following command will do this.

```
[pysparksqlbook@localhost binaries]$ sudo mv  spark-2.3.0-bin-hadoop2.7   /allBigData/spark
```

#### Step 2-2-4\. Changing the Spark Environment File

The Spark environment file possesses all the environment variables required to run Spark. We are going to set the following environmental variables in the environmental file.

*   `HADOOP_CONF_DIR`: Configuration directory of Hadoop.

*   `SPARK_CONF_DIR`: Alternate `conf` directory (Default: `${SPARK_HOME}/conf`)

*   `SPARK_LOG_DIR`: Where log files are stored (Default: `${SPARK_HOME}/log`)

*   `SPARK_WORKER_DIR`: To set the working directory of any worker processes

*   `HIVE_CONF_DIR:` To read data from Hive

At first we have to copy the `spark-env.sh.template` file to `spark-env.sh`. The Spark environment file named `spark-env.sh` is found inside the `spark/conf` file (configuration directory location):

```
[pysparksqlbook@localhost binaries]$ cp /allBigData/spark/conf/spark-env.sh.template /allBigData/spark/conf/spark-env.sh
```

Now let’s open the `spark-env.sh` file .

```
[pysparksqlbook@localhost binaries]$ vim /allBigData/spark/conf/spark-env.sh
```

Now we append the following lines to the end of `spark-env.sh`:

```
export HADOOP_CONF_DIR=/allBigData/hadoop/etc/hadoop/
export SPARK_LOG_DIR=/allBigData/logSpark/
export SPARK_WORKER_DIR=/tmp/spark
export HIVE_CONF_DIR=/allBigData/hive/conf
```

#### Step 2-2-5\. Amending the .bashrc File

In the `.bashrc` file , we have to add a Spark `bin` directory. We can use the following commands to add this.

```
[pysparksqlbook@localhost binaries]$ vim  ~/.bashrc
```

Add the following lines to the `.bashrc` file.

```
export SPARK_HOME=/allBigData/spark
export PATH=$PATH:$SPARK_HOME/bin
```

After this, source the `.bashrc` file.

```
[pysparksqlbook@localhost binaries]$ source  ~/.bashrc
```

#### Step 2-2-6\. Starting the PySpark Shell

We can start the PySpark shell using the `pyspark` script . Discussion about the `pyspark` script will continue in the next recipe.

```
[pysparksqlbook@localhost binaries]$ pyspark
```

We have one more successful installation under our belt. But we have to go further. More installation is required to move through this book. But before all that, it is better to concentrate on the PySpark shell.

## Recipe 2-3\. Use the PySpark Shell

### Problem

You want to use the PySpark shell.

### Solution

The PySpark shell is an interactive shell to interact with PySpark using Python. The PySpark shell can be started using the `pyspark` script. The `pyspark` script can be found at `spark/bin`.

### How It Works

The PySpark shell can be started as follows.

```
[pysparksqlbook@localhost binaries]$ pyspark
```

After starting, it will show the screen in Figure [2-1](#469054_1_En_2_Chapter.xhtml#Fig1).

![../images/469054_1_En_2_Chapter/469054_1_En_2_Fig1_HTML.jpg](images/469054_1_En_2_Chapter/469054_1_En_2_Fig1_HTML.jpg)

Figure 2-1

Startup console screen in PySpark

We can observe that, after starting PySpark, it displays lots of information. It displays information about the Python and PySpark versions it is using.

The >>> symbol is Python’s command prompt. Whenever we start the Python shell, we get this symbol. It tells us that we can now write our Python commands. Similarly in PySpark, it tells us that we can now write our Python or PySpark command and see the result.

The PySpark shell works in a similar fashion on a single machine installation and a cluster installation of PySpark.

## Recipe 2-4\. Install Hive on a Single Machine

### Problem

You want to install Hive on a single machine.

### Solution

We discussed Hive in the first chapter. Now it is time to install Hive on our machine. We are going to read data from Hive to PySparkSQL in coming chapters.

### How It Works

Follow these steps to complete the Hive installation.

#### Step 2-4-1\. Downloading Hive

We can download Hive from the Apache Hive website. We can download the Hive `tar.gz` file using the `wget` command, as follows.

```
[pysparksqlbook@localhost binaries]$   wget http://mirrors.fibergrid.in/apache/hive/stable-2/apache-hive-2.3.3-bin.tar.gz
```

Here is the output:

```
--2018-06-26 18:24:09--  http://mirrors.fibergrid.in/apache/hive/stable-2/apache-hive-2.3.3-bin.tar.gz
Resolving mirrors.fibergrid.in (mirrors.fibergrid.in)... 103.116.36.9, 2402:f4c0::9
Connecting to mirrors.fibergrid.in (mirrors.fibergrid.in)|103.116.36.9|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 232229830 (221M) [application/x-gzip]
Saving to: 'apache-hive-2.3.3-bin.tar.gz'
100%[======================================>] 232,229,830 1.04MB/s   in 4m 29s
2018-06-26 18:28:39 (842 KB/s) - 'apache-hive-2.3.3-bin.tar.gz' saved [232229830/232229830]
```

#### Step 2-4-2\. Extracting Hive

We have downloaded the `apache-hive-2.3.3-bin.tar.gz` file. It is a `.tar.gz` file, so we have to extract it. We can extract it using the `tar` command as follows.

```
[pysparksqlbook@localhost binaries]$ tar xvzf   apache-hive-2.3.3-bin.tar.gz
```

#### Step 2-4-3\. Moving the Extracted Hive Directory

```
[pysparksqlbook@localhost binaries]$ sudo mv apache-hive-2.3.3-bin /allBigData/hive
```

#### Step 2-4-4\. Updating hive-site.xml

Hive is dispatched with an embedded Derby database for metastore. The Derby database is memory-less. Hence, it is better to provide a definite location for it. We provide that location in `hive-site.xml`. For that, we have to move `hive-default.xml.template` to `hive-site.xml`.

```
[pysparksqlbook@localhost binaries]$ mv /allBigData/hive/conf/hive-default.xml.template /allBigData/hive/conf/hive-default.xml.templatehive-site.xml
```

Then open `hive-site.xml` and update the following:

```
[pysparksqlbook@localhost binaries]$ vim /allBigData/hive/conf/hive-site.xml
```

Either add the following line to the end of `hive-site.xml` or change `javax.jdo.option.ConnectionURL` in the `hive-site.xml` file.

```
javax.jdo.option.ConnectionURL
jdbc:derby:;databaseName=/allBigData/hive/metastore/metastore_db;create=true
```

After that, we have to provide `HADOOP_HOME` to the `hive_env.sh` file. The following code shows the command to achieve this.

```
[pysparksqlbook@localhost binaries]$ mv /allBigData/hive/conf/hive-env.sh.template /allBigData/hive/conf/hive-env.sh
[pysparksqlbook@localhost binaries]$ vim  /allBigData/hive/conf/hive-env.sh
```

And in `hive-env.sh`, add the following line.

```
# Set HADOOP_HOME to point to a specific hadoop install directory
HADOOP_HOME=/allBigData/hadoop
```

#### Step 2-4-5\. Updating the .bashrc File

Open the `.bashrc` file . This file stays in the home directory.

```
[pysparksqlbook@localhost binaries]$ vim  ~/.bashrc
```

Add the following lines to the `.bashrc` file:

```
####################Hive Parameters ######################
export HIVE_HOME=/allBigData/hive
export PATH=$PATH:$HIVE_HOME/bin
```

Now source the `.bashrc` file using the following command.

```
[pysparksqlbook@localhost binaries]$ source ~/.bashrc
```

#### Step 2-4-6\. Creating Datawarehouse Directories of Hive

Now we have to create datawarehouse directories. This datawarehouse directory is used by Hive to place the data files:

```
[pysparksqlbook@localhost binaries]$hadoop fs -mkdir -p /user/hive/warehouse
[pysparksqlbook@localhost binaries]$hadoop fs -mkdir -p /tmp
[pysparksqlbook@localhost binaries]$hadoop fs -chmod g+w /user/hive/warehouse
[pysparksqlbook@localhost binaries]$hadoop fs -chmod g+w /tmp
```

The `/user/hive/warehouse` directory is the Hive warehouse directory.

#### Step 2-4-7\. Initiating the Metastore Database

Sometimes it is necessary to initiate schema. You might be thinking, schema of what? We know that Hive stores metadata of tables in a relational database. For the time being, we are going to use the Derby database as a metastore database of Hive. Then, in coming recipes, we are going to connect our Hive to an external PostgreSQL. In Ubuntu, Hive installation works without this command. But in CentOS, I found it indispensable to run. Without the following command, Hive was throwing errors.

```
[pysparksqlbook@localhost  binaries]$ schematool -initSchema -dbType derby
```

#### Step 2-4-8\. Checking the Hive Installation

Now Hive has been installed. We should check our work success. We can start the Hive shell using the following command.

```
[pysparksqlbook@localhost binaries]$ hive
```

After this command, we will find that the Hive shell has been opened as follows:

```
hive>
```

## Recipe 2-5\. Install PostgreSQL

### Problem

You want to install PostgreSQL.

### Solution

PostgreSQL is a Relational Database Management System. It was developed at the University of California. It comes under the PostgreSQL License. It provides permission to use, modify, and distribute under the PostgreSQL license. PostgreSQL can run on MacOS X and UNIX-like systems such as Red Hat, Ubuntu, etc. We are going to install it on CentOS.

We are going to use our PostgreSQL in two ways. We will use PostgreSQL as a metastore database for Hive. After having an external database as the metastore, we will be able to read data from the existing Hive easily. The second use of this RDBMS installation is to read data from PostgreSQL, and after analysis, we will save our result to PostgreSQL.

Installing PostgreSQL can be done with source code, but we are going to install it using the command-line `yum` installer.

### How It Works

Follow these steps to complete the PostgreSQL installation.

#### Step 2-5-1\. Installing PostgreSQL

PostgreSQL can be installed using the `yum` installer. The following code will install PostgreSQL.

```
[pysparksqlbook@localhost binaries]$ sudo yum install postgresql-server postgresql-contrib
[sudo] password for pysparksqlbook:
```

#### Step 2-5-2\. Initializing the Database

PostgreSQL can be utilized with a utility called `initdb` to initialize the database. If we don’t initialize the database, we cannot use it.

At the time of database initialization, we can also specify the data file of the database.

After installing PostgreSQL, we have to initialize it. The database can be initialized using the following command.

```
[pysparksqlbook@localhost binaries]$ sudo postgresql-setup initdb
```

Here is the output:

```
[sudo] password for pysparksqlbook:
Initializing database ... OK
```

#### Step 2-5-3\. Enabling and Starting the Database

```
[pysparksqlbook@localhost binaries]$ sudo systemctl enable postgresql
[pysparksqlbook@localhost binaries]$ sudo systemctl start postgresql
[pysparksqlbook@localhost binaries]$ sudo -i -u postgres
```

Here is the output:

```
[sudo] password for pysparksqlbook:
-bash-4.2$ psql
psql (9.2.24)
Type "help" for help.
postgres=#
```

### Note

We can get the installation procedure at the following site: [`https://wiki.postgresql.org/wiki/YUM_Installation`](https://wiki.postgresql.org/wiki/YUM_Installation) .

## Recipe 2-6\. Configure the Hive Metastore on PostgreSQL

### Problem

You want to configure Hive metastore on PostgreSQL.

### Solution

As we know, Hive puts a metadata of tables in a relational database. We have already installed Hive, which has an embedded metastore. Hive uses the Derby Relational Database System for a metastore. In coming chapters, we have to read existing Hive tables from PySpark.

Configuration of a Hive metastore on PostgreSQL requires us to populate tables in the PostgreSQL database. These tables will hold metadata of Hive tables. After this, we have to configure the Hive property file.

### How It Works

In the following steps, we are going to configure a Hive metastore on the PostgreSQL database. Then our Hive will have metadata in PostgreSQL.

#### Step 2-6-1\. Downloading the PostgreSQL JDBC Connector

We need the JDBC connector so that the Hive process can connect to an external PostgreSQL. We can get the JDBC connector using the following command.

```
[pysparksqlbook@localhost binaries]$ wget https://jdbc.postgresql.org/download/postgresql-42.2.5.jre6.jar
```

#### Step 2-6-2\. Copying the JDBC Connector to the Hive lib Directory

After getting the JDBC connector, we have to put it in the Hive `lib` directory .

```
[pysparksqlbook@localhost binaries]$ cp postgresql-42.2.5.jre6.jar  /allBigData/hive/lib/
```

#### Step 2-6-3\. Connecting to PostgreSQL

```
[pysparksqlbook@localhost binaries]$ sudo -u postgres psql
```

#### Step 2-6-4\. Creating the Required User and Database

In the following lines of this step, we are going to create a PostgreSQL user named `pysparksqlbookUser` . Then we are going to create a database named `pymetastore`. This database is going to hold all the tables related to the Hive metastore.

```
postgres=# CREATE USER pysparksqlbookUser WITH PASSWORD 'pbook';
```

Here is the output:

```
CREATE ROLE
```

Intro.

```
postgres=# CREATE DATABASE pymetastore;
```

Here is the output:

```
CREATE DATABASE
```

The `\c` PostgreSQL command stands for connect. We created our database named `pymetastore`. Now we are going to connect to this database using our `\c` command.

```
postgres=# \c pymetastore;
```

We are now connected to the `pymetastore` database . We can see more PostgreSQL commands using the following link.

[`https://www.postgresql.org/docs/9.2/static/app-psql.html`](https://www.postgresql.org/docs/9.2/static/app-psql.html)

#### Step 2-6-5\. Populating Data in the pymetastore Database

Hive possesses its own PostgreSQL scripts to populate tables for the metastore. The `\i` command reads commands from the PostgreSQL script and executes those commands. In the following command, we are going to run the `hive-txn-schema-2.3.0.postgres.sql` script, which will create all the tables required for the Hive metastore.

```
pymetastore=#  \i /allBigData/hive/scripts/metastore/upgrade/postgres/hive-txn-schema-2.3.0.postgres.sql
```

Here is the output:

```
psql:/allBigData/hive/scripts/metastore/upgrade/postgres/hive-txn-schema-2.3.0.postgres.sql:30: NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "txns_pkey" for table "txns"
CREATE TABLE
CREATE TABLE
INSERT 0 1
psql:/allBigData/hive/scripts/metastore/upgrade/postgres/hive-txn-schema-2.3.0.postgres.sql:69: NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "hive_locks_pkey" for table "hive_locks"
CREATE TABLE
```

#### Step 2-6-6\. Granting Permissions

The following commands will grant some permissions.

```
pymetastore=# grant select, insert,update,delete on public.txns to pysparksqlbookUser;
```

Here is the output:

```
GRANT
pymetastore=# grant select, insert,update,delete on public.txn_components to pysparksqlbookUser;
```

Here is the output:

```
GRANT
pymetastore=# grant select, insert,update,delete on public.completed_txn_components   to pysparksqlbookUser;
```

Here is the output:

```
GRANT
pymetastore=# grant select, insert,update,delete on public.next_txn_id to pysparksqlbookUser;
```

Here is the output:

```
GRANT
pymetastore=# grant select, insert,update,delete on public.hive_locks to pysparksqlbookUser;
```

Here is the output:

```
GRANT
pymetastore=# grant select, insert,update,delete on public.next_lock_id to pysparksqlbookUser;
```

Here is the output:

```
GRANT
pymetastore=# grant select, insert,update,delete on public.compaction_queue to pysparksqlbookUser;
```

Here is the output

```
GRANT
pymetastore=# grant select, insert,update,delete on public.next_compaction_queue_id to pysparksqlbookUser;
```

Here is the output:

```
GRANT
pymetastore=# grant select, insert,update,delete on public.completed_compactions to pysparksqlbookUser;
```

Here is the output:

```
GRANT
pymetastore=# grant select, insert,update,delete on public.aux_table to pysparksqlbookUser;
```

Here is the output:

```
GRANT
```

#### Step 2-6-7\. Changing the pg_hba.conf File

Remember that, in order to update `pg_hba.conf` , you are supposed to be the root user. So first go to the root user. Then open the `pg_hba.conf` file.

```
[root@localhost binaries]# vim /var/lib/pgsql/data/pg_hba.conf
```

Then change all the `peer` and `ident` settings to `trust`.

```
#local   all            all             v                  peer
local   all            all                               trust
# IPv4 local connections:
#host    all            all       127.0.0.1/32            ident
host   all            all       127.0.0.1/32            trust
# IPv6 local connections:
#host    all            all       ::1/128                 ident
host   all            all       ::1/128                 trust
```

More about this change can be found at [`http://stackoverflow.com/questions/2942485/psql-fatal-ident-authentication-failed-for-user-postgres`](http://stackoverflow.com/questions/2942485/psql-fatal-ident-authentication-failed-for-user-postgres) .

Come out of the root user.

#### Step 2-6-8\. Testing Our User

It is better when we are testing that we make sure we are easily able to enter our database using our created user.

```
[pysparksqlbook@localhost binaries]$ psql -h localhost -U pysparksqlbookuser -d pymetastore
```

Here is the output:

```
psql (9.2.24)
Type "help" for help.
pymetastore=>
```

#### Step 2-6-9\. Modifying the hive-site.xml File

We can modify Hive-related configurations in its configuration file `hive-site.xml` . We have to modify the following properties.

*   `javax.jdo.option.ConnectionURL`: Connecting URL to database

*   `javax.jdo.option.ConnectionDriverName`: Connection JDBC driver name

*   `javax.jdo.option.ConnectionUserName`: Database connection user

*   `javax.jdo.option.ConnectionPassword`: Connection password

We can either modify these properties or we can add the following lines at the end of the Hive property file to get the required result.

```
javax.jdo.option.ConnectionURL
jdbc:postgresql://localhost/pymetastore
postgreSQL server metadata store

javax.jdo.option.ConnectionDriverName
org.postgresql.Driver
Driver class of postgreSQL

javax.jdo.option.ConnectionUserName
pysparksqlbookuser
User name to connect to postgreSQL

javax.jdo.option.ConnectionPassword
pbook
password for connecting to PostgreSQL server

```

#### Step 2-6-10\. Starting Hive

We have connected Hive to an external relational database management system. So it is time to start Hive and ensure that everything is fine.

```
[pysparksqlbook@localhost binaries]$ hive
```

Our activities will be reflected in PostgreSQL. Let’s create a database and a table inside that database. The following commands create a database named `apress` and a table called `apressBooks` inside that database.

```
hive> create database apress;
```

Here is the output:

```
OK
Time taken: 1.397 seconds
hive> use apress;
```

Here is the output:

```
OK
Time taken: 0.07 seconds
hive> create table apressBooks (
>      bookName String,
>      bookWriter String
>      )
>      row format delimited
>      fields terminated by ',';
```

Here is the output:

```
OK
Time taken: 0.581 seconds
```

#### Step 2-6-11\. Testing if Metadata Is Created in PostgreSQL

The database and table we created will be reflected in PostgreSQL. We can see the updated data in the TBLS table, as follows.

```
pymetastore=> SELECT * from "TBLS";
| TBL_ID | CREATE_TIME | DB_ID | LAST_ACCESS_TIME | OWNER          | RETENTION | SD_ID |TBL_NAME     | TBL_TYPE      | VIEW_EXPANDED_TEXT | VIEW_ORIGINAL_TEXT
---------+-------------+-------+------------------+--------------+-----------+-------+-------------+---------------+
-----+-------------------
1 |  1482892229 |     6 |                0 | pysparksqlbook|         0 |     1 | apressbooks | MANAGED_TABLE |      |
(1 row)
```

The appreciable work of connecting Hive to an external database is done. In the following recipe, we are going to install Apache Mesos.

## Recipe 2-7\. Connect PySpark to Hive

### Problem

You want to connect PySpark to Hive.

### Solution

PySpark needs a Hive property file to know the configuration parameters of Hive. The Hive property file called `hive-site.xml` stays in the Hive `conf` directory. We simply copy the Hive property file to the Spark `conf` directory. We are done. Now we can start PySpark.

### How It Works

Two steps have been identified to connect PySpark to Hive.

#### Step 2-7-1\. Copying the Hive Property File to the Spark conf Directory

```
[pysparksqlbook@localhost binaries]$cp /allBigData/hive/conf/hive-site.xml /allBigData/spark/conf/
```

#### Step 2-7-2\. Starting PySpark

```
[pysparksqlbook@localhost binaries]$pyspark
```

## Recipe 2-8\. Install MySQL

### Problem

You want to install MySQL Server.

### Solution

We can read data from MySQL using PySparkSQL. We also can save the output of our analysis into a MySQL database. We can install MySQL Server using the `yum` installer.

### How It Works

Follow these steps to complete the MySQL installation.

#### Step 2-8-1\. Installing the MySQL Server

The following command will install the MySQL Server.

```
[pysparksqlbook@localhost binaries]$ sudo yum install mysql-server
```

Here is the output:

```
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
Dependency Installed:
mysql-community-common.x86_64 0:5.6.41-2.el7
perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7
perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7
perl-DBI.x86_64 0:1.627-4.el7
perl-Data-Dumper.x86_64 0:2.145-3.el7
perl-IO-Compress.noarch 0:2.061-2.el7
perl-Net-Daemon.noarch 0:0.48-5.el7
perl-PlRPC.noarch 0:0.2020-14.el7
Replaced:
mariadb.x86_64 1:5.5.60-1.el7_5      mariadb-libs.x86_64 1:5.5.60-1.el7_5
Complete!
```

Finally, we have installed MySQL.

## Recipe 2-9\. Install MongoDB

### Problem

You want to install MongoDB.

### Solution

MongoDB is a NoSQL database. It can be installed using the `yum` installer.

### How It Works

Follow these steps to complete the MongoDB installation.

#### Step 2-9-1\. Installing MongoDB

```
[pysparksqlbook@localhost book]$ sudo vim /etc/yum.repos.d/mongodb-org-4.0.repo
```

Inside this `mongodb-org-4.0.repo` file, copy the following:

```
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
```

### Note

You can get details about the MongoDB installation on Centos from the following link: [`https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/`](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/) .

```
[pysparksqlbook@localhost book]$ sudo yum install -y mongodb-org-4.0.0 mongodb-org-server-4.0.0 mongodb-org-shell-4.0.0 mongodb-org-mongos-4.0.0 mongodb-org-tools-4.0.0
```

Here is the output:

```
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
* updates: centos.excellmedia.net
Installed:
mongodb-org.x86_64 0:4.0.0-1.el7
mongodb-org-mongos.x86_64 0:4.0.0-1.el7
mongodb-org-server.x86_64 0:4.0.0-1.el7
mongodb-org-shell.x86_64 0:4.0.0-1.el7
mongodb-org-tools.x86_64 0:4.0.0-1.el7
Complete!
```

#### Step 2-9-2\. Creating a Data Directory

We are going to create a data directory for MongoDB.

```
[pysparksqlbook@localhost book]$ sudo mkdir -p /data/db
[pysparksqlbook@localhost book]$ sudo chown pysparksqlbook:pysparksqlbook -R /data
```

#### Step 2-9-3\. Starting the MongoDB Server

The MongoDB server will be started using the `mongod` command.

```
[pysparksqlbook@localhost binaries]$ mongod
```

Here is the output:

```
2018-08-26T17:59:42.029-0400 I CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
2018-08-26T17:59:42.051-0400 I CONTROL  [initandlisten] MongoDB starting : pid=22570 port=27017 dbpath=/data/db 64-bit host=localhost.localdomain
2018-08-26T17:59:42.051-0400 I CONTROL  [initandlisten] db version v4.0.0
2018-08-26T18:06:19.459-0400 I NETWORK  [conn1] end connection 127.0.0.1:59690 (0 connections now open)
```

Note that the MongoDB server is listening on `127.0.0.1:59690`. Now we can start the MongoDB shell, called `mongo`.

```
[pysparksqlbook@localhost book]$ mongo
```

Here is the output:

```
MongoDB shell version v4.0.0
connecting to: mongodb://127.0.0.1:27017
To enable free monitoring, run the following command:
db.enableFreeMonitoring()
>
```

## Recipe 2-10\. Install Cassandra

### Problem

You want to install Cassandra.

### Solution

Cassandra is a NoSQL database. It can be installed using the `yum` installer.

### How It Works

Follow these steps to complete the Cassandra installation:

```
[pysparksqlbook@localhost ~]$ sudo vim /etc/yum.repos.d/cassandra.repo
[sudo] password for pysparksqlbook:
```

Copy the following lines in the `cassandra.repo` file:

```
[cassandra]
name=Apache Cassandra
baseurl=https://www.apache.org/dist/cassandra/redhat/311x/
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://www.apache.org/dist/cassandra/KEYS
[pysparksqlbook@localhost ~]$ sudo yum -y install cassandra
```

Here is the output:

```
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
Running transaction
Installing : cassandra-3.11.3-1.noarch        1/1
Verifying  : cassandra-3.11.3-1.noarch        1/1
Installed:
cassandra.noarch 0:3.11.3-1
Complete!
```

Now we start the server:

```
[pysparksqlbook@localhost ~]$ systemctl daemon-reload
[pysparksqlbook@localhost ~]$ systemctl start cassandra
```

# 3. IO in PySpark SQL

Reading data from different types of file formats and saving the result to many data sinks is an inevitable part the data scientist’s job. In this chapter, we are going to learn the following recipes. Through these recipes, we will learn how to read data from different types of data sources and how to save the results of the analysis to different data sinks.

*   Recipe 3-1\. Read a CSV file

*   Recipe 3-2\. Read a JSON file

*   Recipe 3-3\. Save a DataFrame as a CSV file

*   Recipe 3-4\. Save a DataFrame as a JSON file

*   Recipe 3-5\. Read ORC files

*   Recipe 3-6\. Read a Parquet file

*   Recipe 3-7\. Save a DataFrame as an ORC file

*   Recipe 3-8\. Save a DataFrame as a Parquet file

*   Recipe 3-9\. Read data from MySQL

*   Recipe 3-10\. Read data from PostgreSQL

*   Recipe 3-11\. Read data from Cassandra

*   Recipe 3-12\. Read data from MongoDB

*   Recipe 3-13\. Save a DataFrame to MySQL

*   Recipe 3-14\. Save a DataFrame to PostgreSQL

*   Recipe 3-15\. Save a DataFrame to MongoDB

*   Recipe 3-16\. Read data from Apache Hive

## Recipe 3-1\. Read a CSV File

### Problem

You want to read a CSV (comma-separated value) file.

### Solution

CSV files (see Figure [3-1](#469054_1_En_3_Chapter.xhtml#Fig1)) are one of the most used and most popular file types. A data scientist or data engineer will encounter CSV files in their day-to-day experiences.

![../images/469054_1_En_3_Chapter/469054_1_En_3_Fig1_HTML.png](images/469054_1_En_3_Chapter/469054_1_En_3_Fig1_HTML.png)

Figure 3-1

A sample CSV file

This CSV file shown in Figure [3-1](#469054_1_En_3_Chapter.xhtml#Fig1) is named `swimmerData.csv` `.` This tabular form has four columns—`id`, `Gender`, `Occupation`, and `swimTimeInSecond`. The first three columns contain data in the `String` datatype. The last column uses the `float` or `double` datatype.

We can read a CSV file using the `spark.read.csv()` function . Here, `spark` is object of the `SparkSession` class. This function has many arguments but we are going to discuss the more important ones. We specify the path of the CSV file, which has to be read by the `path` argument. The PySpark SQL DataFrame has a tabular structure similar to RDBMS tables. Therefore, we have to specify the schema of the DataFrame. We specify the schema of the DataFrame using the second argument of the `csv()` function, that is, `schema`.

The CSV name is misleading, because these files might have any character as a data field separator. We specify the data field separator using the argument `sep`. If the file has a header, we can specify it using the `header` argument. If the value of the argument header is `None`, this indicates that there is no header in the file. But if there is a header, we must set the value of the `header` argument to `True`. The function is smart and can infer the schema if the `inferSchema` argument has been set to `True`.

### How It Works

Let’s start by creating the schema of the DataFrame.

#### Step 3-1-1\. Creating the Schema of the DataFrame

In our DataFrame, we have four columns. At first, we are going to define the columns. We define columns using the `StructField()` function. PySpark SQL has its own datatypes. All datatypes for PySpark SQL have been defined in the submodule named `pyspark.sql.types`. We have to import everything from `pyspark.sql.types` as follows.

```
In [1]: from pyspark.sql.types import *
```

After importing the required submodule, we are going to define the first column of the DataFrame.

```
In [2]: idColumn = StructField("id",StringType(),True)
```

Let’s look at the arguments of `StructField()`. The first argument is the column name. We provide the column name as `id`. The second argument is the datatype of the elements of the column. The datatype of the first column is `StringType()`. If some ID is missing then some element of a column might be null. The last argument, whose value is `True`, tells you that this column might have null values or missing data.

```
In [3]: genderColumn = StructField("Gender",StringType(),True)
In [4]: OccupationColumn = StructField("Occupation",StringType(),True)
In [5]: swimTimeInSecondColumn = StructField("swimTimeInSecond",DoubleType(),True)
```

We have created a `StructField` for each column. Now we have to create a schema of the full DataFrame. We can create that using a `StructType` object, as the following code line is depicting.

```
In [6]: columnList = [idColumn, genderColumn, OccupationColumn, swimTimeInSecondColumn]
In [7]: swimmerDfSchema = StructType(columnList)
```

`swimmerDfSchema` is the full schema of our DataFrame.

```
In [8]: swimmerDfSchema
```

Here is the output:

```
Out[8]: StructType(List(StructField(id,StringType,true),
StructField(Gender,StringType,true),
StructField(Occupation,StringType,true),
StructField(swimTimeInSecond,DoubleType,true)))
)
```

The schema of the four columns of the DataFrame can be observed by using the `swimmerDfSchema` variable.

#### Step 3-1-2\. Reading a CSV File

We have created our schema. We are now going to read the CSV file.

```
In [9]: swimmerDf = spark.read.csv('data/swimmerData.csv',
...:                           header=True, schema=swimmerDfSchema
...: )
In [10]: swimmerDf.show(4)
```

Here is the output:

```
+---+------+-----------+----------------+
| id|Gender| Occupation|swimTimeInSecond|
+---+------+-----------+----------------+
|id1|  Male| Programmer|           16.73|
|id2|Female|    Manager|           15.56|
|id3|  Male|    Manager|           15.15|
|id4|  Male|RiskAnalyst|           15.27|
+---+------+-----------+----------------+
only showing top 4 rows
In [13]: swimmerDf.printSchema()
```

Here is the output:

```
root
|-- id: string (nullable = true)
|-- Gender: string (nullable = true)
|-- Occupation: string (nullable = true)
|-- swimTimeInSecond: double (nullable = true)
```

We read the file and created the DataFrame.

#### Step 3-1-3\. Reading a CSV File Using the Argument inferSchema Value as True

Now we are going to read the same file, `swimmerData.csv`. This time we are not going to provide the schema. We will let PySpark SQL infer the schema of the DataFrame. If we set the value of the `inferSchema` argument to `True` in the `csv()` function, PySpark SQL will try to infer the schema.

```
In [14]: swimmerDf = spark.read.csv('swimmerData.csv',
header=True, inferSchema=True)
In [15]: swimmerDf.show(4)
+---+------+-----------+----------------+
| id|Gender| Occupation|swimTimeInSecond|
+---+------+-----------+----------------+
|id1|  Male| Programmer|           16.73|
|id2|Female|    Manager|           15.56|
|id3|  Male|    Manager|           15.15|
|id4|  Male|RiskAnalyst|           15.27|
+---+------+-----------+----------------+
only showing top 4 rows
```

The DataFrame called `swimmerDf` has been created. We have used the `inferSchema` argument set to `True`. It is better to check if the schema created the DataFrame. So let let’s print it using the `printSchema()` function.

```
In [16]:  swimmerDf.printSchema()
```

Here is the output:

```
root
|-- id: string (nullable = true)
|-- Gender: string (nullable = true)
|-- Occupation: string (nullable = true)
|-- swimTimeInSecond: double (nullable = true)
```

PySpark SQL has inferred the schema correctly.

## Recipe 3-2\. Read a JSON File

### Problem

You want to read a JSON (JavaScript Object Notation) file.

### Solution

We have been given a JSON data file called `corrData.json` . The contents of the file look like this:

```
{"iv1":5.5,"iv2":8.5,"iv3":9.5}
{"iv1":6.13,"iv2":9.13,"iv3":10.13}
{"iv1":5.92,"iv2":8.92,"iv3":9.92}
{"iv1":6.89,"iv2":9.89,"iv3":10.89}
{"iv1":6.12,"iv2":9.12,"iv3":10.12}
```

Imagine this data in tabular form. We can find that the data has three columns called `iv1`, `iv2`, and `iv3`. Each column has decimal or floating point values.

The `spark.read.json()` function will read JSON files. Like the `csv()` function, there are many arguments in the `json()` function. The first argument is `path` and it determines the location of the file to be read. The second argument is `schema` and it determines the schema of the DataFrame to be created.

### How It Works

Let’s read our `corrData.json` file.

```
In [1]: corrData = spark.read.json(path='corrData.json')
In [2]: corrData.show(6)
```

Here is the output:

```
+---+-----+-----+
|iv1|  iv2|  iv3|
+----+----+-----+
| 5.5| 8.5|  9.5|
|6.13|9.13|10.13|
|5.92|8.92| 9.92|
|6.89|9.89|10.89|
|6.12|9.12|10.12|
|6.32|9.32|10.32|
+----+----+-----+
only showing top 6 rows
```

We have created the DataFrame successfully.

Whenever, we do not provide the schema of DataFrame explicitly. It is better to check the schema of the newly created DataFrame to ensure that everything is as expected.

```
In [3]: corrData.printSchema()
```

Here is the output:

```
root
|-- iv1: double (nullable = true)
|-- iv2: double (nullable = true)
|-- iv3: double (nullable = true)
```

And everything is as expected, as the datatype of each column is `double` or `DoubleType()` . So, while reading a JSON file, if we do not provide the schema, PySpark SQL will infer it.

## Recipe 3-3\. Save a DataFrame as a CSV File

### Problem

You want to save the contents of a DataFrame to a CSV file.

### Solution

Whenever we think to write or save a DataFrame to an external storage system, we can use the `DataFrameWriter` class and the methods defined in it. We can access `DataFrameWriter` using `DataFrame.write`. So, if we want to save our DataFrame as a CSV file, we have to use the `DataFrame.write.csv()` function.

Similar to the `spark.read.csv()` function, the `DataFrame.write.csv()` function has many arguments. Let’s discuss its three arguments—`path`, `sep`, and `header`. The `path` argument defines the directory where DataFrame will be written. We can specify the data field separator using the `sep` argument. If the value of the `header` argument is `True`, the header of the DataFrame will be written as the first line in the CSV file.

We are going to write the `corrData` DataFrame into the `csvFileDir` directory. We created the `corrData` DataFrame in Recipe 3-2.

### Note

You can read more about the `DataFrameWriter` class from the following link

[`https://spark.apache.org/docs/2.3.0/api/python/_modules/pyspark/sql/readwriter.html#DataFrameWriter`](https://spark.apache.org/docs/2.3.0/api/python/_modules/pyspark/sql/readwriter.html%2523DataFrameWriter)

### How It Works

The following line of code will write the DataFrame to a CSV file.

```
corrData.write.csv(path='csvFileDir', header=True,sep=',')
```

Let’s see what’s inside the `csvFileDir` directory. We can check using the `bash ls` command as follows:

```
csvFileDir$ ls
```

Here is the output:

```
part-00000-eb3df2e6-8098-488d-be22-5e9db4a5cb08-c000.csv
_SUCCESS
```

We can see there are two files in the directory. Let’s first discuss the second file, called `_SUCCESS`. This file just tells that our `write` operation is successful. The actual contents of the DataFrame are inside the `part-00000-eb3df2e6-8098-488d-be22-5e9db4a5cb08-c000.csv` file. Now let’s investigate the contents of that file.

```
$ head -5 part-00000-eb3df2e6-8098-488d-be22-5e9db4a5cb08-c000.csv
```

Here is the output:

```
iv1,iv2,iv3
5.5,8.5,9.5
6.13,9.13,10.13
5.92,8.92,9.92
6.89,9.89,10.89
```

Now it is confirmed that the contents have been written to a CSV file.

## Recipe 3-4\. Save a DataFrame as a JSON File

### Problem

You want to save the contents of a DataFrame as a JSON file.

### Solution

In order to save a DataFrame as a JSON file, we are going to use the `DataFrameWriter` class function called `json()`. We are going to save the `swimmerDf` DataFrame as a JSON file.

### How It Works

The following line will easily save our DataFrame as a JSON file in the `jsonData` directory.

```
In [1]: swimmerDf.write.json(path='jsonData')
```

Now let’s visualize the contents of the `jsonData` directory.

```
$ ls
```

Here is the output:

```
part-00000-51e76de8-127f-4549-84a8-7ea972632a4d-c000.json
_SUCCESS
```

The file called `part-00000-51e76de8-127f-4549-84a8-7ea972632a4d-c000.json` contains the `swimmerDf` DataFrame data.

We can see that the data has been written in the JSON format correctly. We are going to use the `head shell` command to print the first four records.

```
$ head -4 part-00000-51e76de8-127f-4549-84a8-7ea972632a4d-c000.json
```

Here is the output:

```
{"id":"id1","Gender":"Male","Occupation":"Programmer","swimTimeInSecond":16.73}
{"id":"id2","Gender":"Female","Occupation":"Manager","swimTimeInSecond":15.56}
{"id":"id3","Gender":"Male","Occupation":"Manager","swimTimeInSecond":15.15}
{"id":"id4","Gender":"Male","Occupation":"RiskAnalyst","swimTimeInSecond":15.27}
```

PySpark SQL saved the DataFrame’s contents into the JSON file correctly.

## Recipe 3-5\. Read ORC Files

### Problem

You want to read an ORC (Optimized Row Columnar) file.

### Solution

The ORC file format was mainly developed for Apache Hive to make Hive queries faster on datasets in the ORC format. See Figure [3-2](#469054_1_En_3_Chapter.xhtml#Fig2).

![../images/469054_1_En_3_Chapter/469054_1_En_3_Fig2_HTML.jpg](images/469054_1_En_3_Chapter/469054_1_En_3_Fig2_HTML.jpg)

Figure 3-2

A sample ORC file

The table data shown in Figure [3-2](#469054_1_En_3_Chapter.xhtml#Fig2) is in an ORC file in the `duplicateData` directory. We have to read it.

We are going to read the ORC file using the `spark.read.orc()` function. Remember that `spark` is the object of the `SparkSession` class.

### Note

More about ORC files can be found from the following link

[`https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC`](https://cwiki.apache.org/confluence/display/Hive/LanguageManual%252BORC)

### How It Works

We are going to read our data from the ORC file inside the `duplicateData` directory.

```
In [1]: duplicateDataDf = spark.read.orc(path='duplicateData')
In [2]: duplicateDataDf.show(6)
```

Here is the output:

```
+---+---+-----+
|iv1|iv2|  iv3|
+---+---+-----+
| c1| d2|  9.8|
| c1| d2| 8.36|
| c1| d2| 9.06|
| c1| d2|11.15|
| c1| d2| 6.26|
| c2| d2| 8.74|
+---+---+-----+
only showing top 6 rows
```

## Recipe 3-6\. Read a Parquet File

### Problem

You want to read a Parquet file.

### Solution

Apache Parquet is an open source file format developed for Apache Hadoop. It uses a columnar data storage format. It provides encoding to the data and efficient data compression.

We have a Parquet file in a directory called `temperatureData`. We can read the Parquet file using the `spark.read.parquet()` function of PySpark SQL.

### Note

You can read more about Parquet files from the following links

[`https://parquet.apache.org/documentation/latest/`](https://parquet.apache.org/documentation/latest/)

[`https://en.wikipedia.org/wiki/Apache_Parquet`](https://en.wikipedia.org/wiki/Apache_Parquet)

### How It Works

We have been given data in a Parquet file. The name of the data directory is `temperatureData`.

```
In [2]: tempDf = spark.read.parquet('temperatureData')
In [3]: tempDf.show(6)
```

Here is the output:

```
+----+-------------+
| day|tempInCelsius|
+----+-------------+
|day1|         12.2|
|day2|         13.1|
|day3|         12.9|
|day4|         11.9|
|day5|         14.0|
|day6|         13.9|
+----+-------------+
only showing top 6 rows
```

We can see that we have the DataFrame.

## Recipe 3-7\. Save a DataFrame as an ORC File

### Problem

You want to save a DataFrame as an ORC file.

### Solution

We are going to save the `swimmerDf` DataFrame to an ORC file. We are going to save it in a directory called `orcData`. We are going to use the `orc()` function of the `DataFrameWriter` class to save the DataFrame as an ORC file.

### How It Works

The following line of code will save our `swimmerDf` DataFrame content to an ORC file in the `orcData` directory.

```
In [1]: swimmerDf.write.orc(path='orcData')
```

Let’s see what’s inside the `orcData` directory.

```
$ ls
```

Here is the output:

```
part-00000-4252c3c8-5f7d-48b2-8bc0-48426cb8d2e4-c000.snappy.orc  _SUCCESS
```

## Recipe 3-8\. Save a DataFrame as a Parquet File

### Problem

You want to save a DataFrame as a Parquet file.

### Solution

We created the `duplicateDataDf` DataFrame in Recipe 3-5\. We are going to use the `parquet()` function of the `DataFrameWrite` class.

### How It Works

Using the following line of code, we can write the contents of the DataFrame into a Parquet file in the `parqData` directory.

```
In [1]: duplicateDataDf.write.parquet(path='parqData')
```

In order to see the contents of the `parqData` directory, we are going to use the `ls` command.

```
$ ls
```

Here is the output:

```
part-00000-d44eb594-46da-495a-88e9-934ca1eff270-c000.snappy.parquet _SUCCESS
```

Data has been written to the file called `part-00000-d44eb594-46da-495a-88e9-934ca1eff270-c000.snappy.parquet`.

## Recipe 3-9\. Read Data from MySQL

### Problem

You want to read a table of data from MySQL.

### Solution

MySQL is one of the most popular relational database management systems there is. Many times, we might have to fetch data from MySQL to perform analysis by PySpark SQL. In order to connect to MySQL, we need a MySQL JDBC connector. The following command line will start the PySpark shell with a MySQL JDBC connector.

```
pyspark --driver-class-path  ~/.ivy2/jars/mysql_mysql-connector-java-8.0.12.jar --packages mysql:mysql-connector-java:8.0.12
```

### How It Works

The admission data is contained in the `ucbdata` table of the `pysparksqlbook` database of MySQL. We can read this data using the following line of code.

```
In [1]: dbURL = "jdbc:mysql://localhost/pysparksqlbook"
```

The `ucbdata` table has been created by the root user.

```
In [2]: ucbDataFrame = spark.read.format("jdbc").options(url = dbURL, database ='pysparksqlbook', dbtable ='ucbdata', user="root", password="").load();
```

The `options()` function is used to set different options. In the `options()` function, we set the value of `url`, the value of `database`, the value of `table`, and the user and password of the database.

```
In [3]: ucbDataFrame.show()
```

Here is the output:

```
+--------+------+----------+---------+
|   admit|gender|department|frequency|
+--------+------+----------+---------+
|Admitted| Male |         A|      512|
|Rejected| Male |         A|      313|
|Admitted|Female|         A|       89|
|Rejected|Female|         A|       19|
|Admitted| Male |         B|      353|
|Rejected| Male |         B|      207|
|Admitted|Female|         B|       17|
|Rejected|Female|         B|        8|
|Admitted| Male |         C|      120|
|Rejected| Male |         C|      205|
|Admitted|Female|         C|      202|
|Rejected|Female|         C|      391|
|Admitted| Male |         D|      138|
|Rejected| Male |         D|      279|
|Admitted|Female|         D|      131|
|Rejected|Female|         D|      244|
|Admitted| Male |         E|       53|
|Rejected| Male |         E|      138|
|Admitted|Female|         E|       94|
|Rejected|Female|         E|      299|
+--------+------+----------+---------+
only showing top 20 rows
```

## Recipe 3-10\. Read Data from PostgreSQL

### Problem

You want to read a table of data from a PostgreSQL database.

### Solution

We have the `firstverticaltable` table in PostgreSQL. We can see the data using the `select` command as follows.

```
pysparksqldb=#  select * from firstverticaltable;
```

Here is the output:

```
iv1|  iv2|  iv3
-----+-----+-----
9|11.43|10.25
10.26| 8.35| 9.94
9.84| 9.28| 9.22
11.77|10.18|11.02
(4 rows)
```

We have to read the `firstverticaltable` table using PySpark SQL. We know that in order to read data from PostgreSQL, we need a PostgreSQL JDBC connector. The following command will serve that purpose.

```
$pyspark --driver-class-path  ~/.ivy2/jars/org.postgresql_postgresql-42.2.4.jar  --packages org.postgresql:postgresql:42.2.4
```

Now in the PySpark shell, we can write commands to read data from the PostgreSQL database.

### How It Works

Let’s read the data table from PostgreSQL and create the DataFrames.

First we are going to define `dbURL`.

```
In [1]: dbURL = "jdbc:postgresql://localhost/pysparksqldb?user=postgres&password=""
In [2]: verticalDfOne = spark.read.format("jdbc").options(url = dbURL, database ='pysparksqldb', dbtable ='firstverticaltable').load();
```

Again here, we are setting different options in the `options()` function as in the previous recipe.

```
In [3]: verticalDfOne.show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.26| 8.35| 9.94|
| 9.84| 9.28| 9.22|
|11.77|10.18|11.02|
+-----+-----+-----+
```

We have read the `firstverticaltable` table and created the `verticalDfOne` DataFrame.

## Recipe 3-11\. Read Data from Cassandra

### Problem

You want to read a table of data from a Cassandra database.

### Solution

Cassandra is a popular NoSQL database. Figure [3-3](#469054_1_En_3_Chapter.xhtml#Fig3) shows a data table in Cassandra. The `students` table is in the `pysparksqlbook` keyspace.

![../images/469054_1_En_3_Chapter/469054_1_En_3_Fig3_HTML.jpg](images/469054_1_En_3_Chapter/469054_1_En_3_Fig3_HTML.jpg)

Figure 3-3

The students table in Cassandra

We have to read the `students` table using PySpark SQL. But we need a Cassandra connector. Therefore, we have to start our PySpark shell using the following command.

```
$ pyspark  --packages com.datastax.spark:spark-cassandra-connector_2.11:2.3.0
```

### Note

You can read more about connecting PySpark SQL to Cassandra on the following web page

[`https://github.com/datastax/spark-cassandra-connector/blob/master/doc/15_python.md`](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/15_python.md)

### How It Works

The following lines will read data tables from the Cassandra database.

```
In [1]: studentsDf = spark.read.format("org.apache.spark.sql.cassandra").options( keyspace="pysparksqlbook", table="students").load()
```

After reading data from Cassandra, let’s print it to verify that we have created the DataFrame.

```
In [2]: studentsDf.show()
```

Here is the output:

```
+---------+------+-------+
|studentid|gender|   name|
+---------+------+-------+
|      si3|     F|  Julie|
|      si2|     F|  Maria|
|      si1|     M|  Robin|
|      si6|     M|William|
|      si4|     M|    Bob|
+---------+------+-------+
```

## Recipe 3-12\. Read Data from MongoDB

### Problem

You want to read a collection of data from MongoDB.

### Solution

In the context of MongoDB, a collection is a table. In this case, we have a collection called `restaurantSurvey` in the `pysparksqlbook` database in MongoDB. Using `find()` on a collection will return all the documents in that collection. A document is what MongoDB calls a record.

```
> db.restaurantSurvey.find().pretty().limit(5)
```

Here is the output:

```
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78a"),
"Gender" : "Male",
"Vote" : "Yes"
}
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78b"),
"Gender" : "Male",
"Vote" : "Yes"
}
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78c"),
"Gender" : "Male",
"Vote" : "No"
}
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78d"),
"Gender" : "Male",
"Vote" : "DoNotKnow"
}
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78e"),
"Gender" : "Male",
"Vote" : "Yes"
}
```

We have printed the first five records from our `restaurantSurvey` collection. We have to read this collection data using PySpark SQL and create a DataFrame.

Reading from MongoDB using PySpark SQL will look for some extra packages. The extra package is nothing but a MongoDB connector. We can use the following command to start the PySpark shell to include the extra package to read data from MongoDB.

```
$pyspark --conf "spark.mongodb.input.uri=mongodb://127.0.0.1/pysparksqlbook. restaurantSurvey?readPreference=primaryPreferred" --packages org.mongodb.spark:mongo-spark-connector_2.11:2.3.0
```

### Note

You can read about reading data from MongoDB in the following links

[`https://docs.mongodb.com/spark-connector/master/python-api/`](https://docs.mongodb.com/spark-connector/master/python-api/)

[`https://docs.mongodb.com/spark-connector/master/python/read-from-mongodb/`](https://docs.mongodb.com/spark-connector/master/python/read-from-mongodb/)

### How It Works

We have the restaurant survey data in the `restaurantSurvey` collection of the `pysparksqlbook` database in MongoDB. The following code will read our data from MongoDB.

```
In [1]: surveyDf = spark.read.format("com.mongodb.spark.sql.DefaultSource").option("uri","mongodb://127.0.0.1/pysparksqlbook.restaurantSurvey").load()
```

We are setting one option. That is the URI of the collection in MongoDB.

```
In [2]: surveyDf.show(5)
```

Here is the output:

```
+------+---------+--------------------+
|Gender|     Vote|                 _id|
+------+---------+--------------------+
|  Male|      Yes|[5ba7e6a259acc01f...|
|  Male|      Yes|[5ba7e6a259acc01f...|
|  Male|       No|[5ba7e6a259acc01f...|
|  Male|DoNotKnow|[5ba7e6a259acc01f...|
|  Male|      Yes|[5ba7e6a259acc01f...|
+------+---------+--------------------+
only showing top 5 rows
```

We have read our data.

## Recipe 3-13\. Save a DataFrame to MySQL

### Problem

You want to save the contents of a DataFrame to MySQL.

### Solution

In order to save a DataFrame to MySQL, we are going to use the `write()` function of the `DataFrameWriter` class.

Remember that we have to start the PySpark shell with the MySQL connector.

```
$pyspark --driver-class-path  ~/.ivy2/jars/mysql_mysql-connector-java-8.0.12.jar --packages mysql:mysql-connector-java:8.0.12
```

### How It Works

#### Step 3-13-1\. Creating a DataFrame

Let’s create a DataFrame. In order to create a DataFrame, we need the `Row` class. The `Row` class is in the `pyspark.sql` submodule. Let’s import the `Row` class now.

```
In [1]: from pyspark.sql import Row
```

We are creating a DataFrame on the fly using the following command.

```
In [2]: ourDf = spark.createDataFrame([
...:              Row(id=1, value=10.0),
...:              Row(id=2, value=42.0),
...:              Row(id=3, value=32.0)
...:              ])
In [3]: ourDf.show()
```

Here is the output:

```
+--+-----+
|id|value|
+--+-----+
| 1| 10.0|
| 2| 42.0|
| 3| 32.0|
+--+-----+
```

So the `ourDf` DataFrame is ready to be saved.

#### Step 3-13-2\. Saving the DataFrame into a MySQL Database

We are going to define the `url` database first and then save it into a MySQL database.

```
In [4]: dbURL = "jdbc:mysql://localhost/pysparksqlbook"
In [5]: ourDf.write.format("jdbc").options(url = dbURL, database ='pysparksqlbook', dbtable ='mytab', user="root", password="").save()
```

After saving the DataFrame, let’s check if it has been saved properly. We have saved our DataFrame in the `mytab` table, which is inside the `pysparksqlbook` database.

```
mysql> use pysparksqlbook;
```

Here is the output:

```
Database changed
```

Using the `select` command , we can visualize the contents of the `mytab` table.

```
mysql> select * from mytab;
```

Here is the output:

```
+--+-----+
|id|value|
+--+-----+
| 1|   10|
| 3|   32|
| 2|   42|
+--+-----+
3 rows in set (0.00 sec)
```

We are successful.

## Recipe 3-14\. Save a DataFrame to PostgreSQL

### Problem

You want to save a DataFrame into PostgreSQL.

### Solution

Saving DataFrame content to PostgreSQL requires a PostgreSQL JDBC connector.

We can use the `write()` function of the `DataFrameWriter` class. In order to use the PostgreSQL JDBC connector, we have to start the PySpark shell using the following command.

```
$pyspark --driver-class-path  ~/.ivy2/jars/org.postgresql_postgresql-42.2.4.jar  --packages org.postgresql:postgresql:42.2.4
```

### How It Works

#### Step 3-14-1\. Creating a DataFrame

We are going to create a dummy DataFrame.

```
In [1]: from pyspark.sql import Row
In [2]: ourDf = spark.createDataFrame([
...:                   Row(iv1=1.2, iv2=10.0),
...:                   Row(iv1=1.3, iv2=42.0),
...:                   Row(iv1=1.5, iv2=32.0)
...:                   ])
In [3]: ourDf.show()
```

Here is the output:

```
+---+----+
|iv1| iv2|
+---+----+
|1.2|10.0|
|1.3|42.0|
|1.5|32.0|
+---+----+
```

#### Step 3-14-2\. Saving a DataFrame into a PostgreSQL Database

We are going to define the `url` database first and then save it into the PostgreSQL database.

```
In [4]: dbURL = "jdbc:postgresql://localhost/pysparksqldb?user=postgres&password=""
```

We are saving the DataFrame contents to the `mytab` table.

```
In [5]: ourDf.write.format("jdbc").options(url = dbURL, database ='pysparksqlbook', dbtable ='mytab').save()
```

Let’s check if it has been saved into the PostgreSQL database.

```
postgres=#  \c pysparksqldb;
pysparksqldb=# select * from mytab;
```

Here is the output:

```
iv1|iv2
---+---
1.3| 42
1.2| 10
1.5| 32
(3 rows)
```

## Recipe 3-15\. Save DataFrame Contents to MongoDB

### Problem

You want to save DataFrame contents as a collection in MongoDB.

### Solution

In order to save the DataFrame to MongoDB, we have to use the connector associated with it.

We are going to use the following line to start PySpark with a MongoDB connector.

```
$ pyspark --conf "spark.mongodb.input.uri=mongodb://127.0.0.1/pysparksqlbook?readPreference=primaryPreferred" --packages org.mongodb.spark:mongo-spark-connector_2.11:2.3.0
```

### Note

You can read more about writing data to MongoDB using PySpark from the following link.

[`https://docs.mongodb.com/spark-connector/master/python/write-to-mongodb/`](https://docs.mongodb.com/spark-connector/master/python/write-to-mongodb/)

### How It Works

In Recipe 3-14, we used a DataFrame called `ourDf`. Let’s print its contents.

```
In [1]: ourDf.show()
```

Here is the output:

```
+---+----+
|iv1| iv2|
+---+----+
|1.2|10.0|
|1.3|42.0|
|1.5|32.0|
+---+----+
```

We are going to write the contents of our `ourDf` DataFrame to MongoDB. We are going to write this DataFrame to a collection called `mytab` in the `pysparksqlbook` database. The following command will perform this task.

```
In [2]: ourDf.write.format("com.mongodb.spark.sql.DefaultSource").option("database",
...: "pysparksqlbook").option("collection", "mytab").option("uri",dbUrl).save()
```

We have written the DataFrame to MongoDB. Let’s check our data in the `mytab` collection, which is inside `pysparksqlbook`.

```
> use pysparksqlbook;
```

Here is the output:

```
switched to db pysparksqlbook
> db.mytab.find().pretty()
```

Here is the output:

```
{ "_id" : ObjectId("5bb51e00f17eb43aee74cb8f"), "iv1" : 1.2, "iv2" : 10 }
{ "_id" : ObjectId("5bb51e00f17eb43aee74cb91"), "iv1" : 1.3, "iv2" : 42 }
{ "_id" : ObjectId("5bb51e00f17eb43aee74cb90"), "iv1" : 1.5, "iv2" : 32 }
```

Finally, we have our DataFrame content in MongoDB.

## Recipe 3-16\. Read Data from Apache Hive

### Problem

You want to read a table of data from Apache Hive.

### Solution

We have a table called `filamentdata` in Hive. We have to read this data using PySpark SQL from Apache Hive. Let’s go through the whole process. First we are going to create a table in Hive and upload data into it. Let’s start by creating a table called `filamentdata`. We will create our table in the `apress` database of Hive. We can display all the databases in Hive using `show`.

```
hive> show databases;
```

Here is the output:

```
OK
apress
default
Time taken: 3.275 seconds, Fetched: 2 row(s)
```

The database is called `apress`, so we have to specify this database using the `use` command.

```
hive> use apress;
```

Here is the output:

```
OK
Time taken: 0.125 seconds
```

After using the database, we are going to create a table named `filamenttable` using the following command.

```
hive> create table filamenttable (
>  filamenttype string,
>  bulbpower string,
>   lifeinhours float
>)
> row format delimited
> fields terminated by ',';
```

We have created a Hive table with three columns. The first column is `filamenttype`, whose values are of `string` type. The second column of our table is `bulbpower` with a datatype of `string`. The third column is `lifeinhours` of float `type`. Now we can display our table creation using the `show` command.

```
hive> show tables;
```

Here is the output:

```
OK
filamenttable
Time taken: 0.118 seconds, Fetched: 1 row(s)
```

The required table has been created successfully. Let’s load the data into the table we created. We are loading data into Hive from a local directory. The data is being loaded using the `load` clause with `local`. This tells Hive that the data is being loaded from a local directory, not from HDFS.

```
hive> load data local inpath 'filamentData.csv' overwrite into table filamenttable;
```

Here is the output:

```
Loading data to table apress.filamenttable
OK
Time taken: 5.39 seconds
```

After the data load, we can query over the table. We can display certain rows using `select` and `limit` to limit the number of rows.

```
hive> select * from filamenttable limit 5;
```

Here is the output:

```
OK
filamentA    100W    605.0
filamentB    100W    683.0
filamentB    100W    691.0
filamentB    200W    561.0
filamentA    200W    530.0
Time
taken: 0.532 seconds, Fetched: 5 row(s)
```

We have displayed some rows of the `filamenttable` table. We have to read this table data using PySpark SQL.

We can read the table from Hive using PySpark SQL and the `spark.table()` function.

### How It Works

In the `table` function , we have to provide the name of the table. The table name is provided in the format `<databaseName>.<tableName>`. In our case, the database’s name is `apress` and the table’s name is `filamenttable`. Therefore, the argument value to the table function will be `apress.filamenttable`.

```
In [1]: FilamentDataFrame = spark.table('apress.filamenttable')
In [2]: FilamentDataFrame.show(5)
```

Here is the output:

```
+------------+---------+-----------+
|filamenttype|bulbpower|lifeinhours|
+------------+---------+-----------+
|   filamentA|     100W|      605.0|
|   filamentB|     100W|      683.0|
|   filamentB|     100W|      691.0|
|   filamentB|     200W|      561.0|
|   filamentA|     200W|      530.0|
+------------+---------+-----------+
only showing top 5 rows
```

And finally we created a DataFrame from the table in Apache Hive.

# 4. Operations on PySpark SQL DataFrames

Once we create DataFrames, we can perform many operations on them. Some operations will reshape a DataFrame to add more features to it or remove unwanted data. Operations on DataFrames are also helpful in getting insights of the data using exploratory analysis.

This chapter discusses DataFrame filtering, data transformation, column deletion, and many related operations on a PySpark SQL DataFrame.

We cover the following recipes. Each recipe is useful and interesting in its own way. I suggest you go through each one once.

*   Recipe 4-1\. Transform values in a column of a DataFrame

*   Recipe 4-2\. Select columns from a DataFrame

*   Recipe 4-3\. Filter rows from a DataFrame

*   Recipe 4-4\. Delete a column from an existing DataFrame

*   Recipe 4-5\. Create and use a PySpark SQL UDF

*   Recipe 4-6\. Label data

*   Recipe 4-7\. Perform descriptive statistics on a column of a DataFrame

*   Recipe 4-8\. Calculate covariance

*   Recipe 4-9\. Calculate correlation

*   Recipe 4-10\. Describe a DataFrame

*   Recipe 4-11\. Sort data in a DataFrame

*   Recipe 4-12\. Sort data partition-wise

*   Recipe 4-13\. Remove duplicate records from a DataFrame

*   Recipe 4-14\. Sample records

*   Recipe 4-15\. Find frequent items

### Note

My intention behind repeating some lines of code is to provide less distraction while you are going through the step-by-step solution of a problem. Missing code lines force readers to go to previous chapters or to previous pages to make connections. When all the code chunks are together, the flow is clear and logical and it’s easier to understand the solution.

## Recipe 4-1\. Transform Values in a Column of a DataFrame

### Problem

You want to apply a transformation operation on a column in a DataFrame.

### Solution

There is a swimming competition. The length of the swimming pool is 20 meters. The number of participants in the competition is 12\. The swim time in seconds has been noted. Table [4-1](#469054_1_En_4_Chapter.xhtml#Tab1) depicts the details of the participants.

Table 4-1

Sample Data for the Swimming Competition

| id | Gender | Occupation | swimTimeInSecond |
| --- | --- | --- | --- |
| id1 | Male | Programmer | 16.73 |
| id2 | Female | Manager | 15.56 |
| id3 | Male | Manager | 15.15 |
| id4 | Male | RiskAnalyst | 15.27 |
| id5 | Male | Programmer | 15.65 |
| id6 | Male | RiskAnalyst | 15.74 |
| id7 | Female | Programmer | 16.8 |
| id8 | Male | Manager | 17.11 |
| id9 | Female | Programmer | 16.83 |
| id10 | Female | RiskAnalyst | 16.34 |
| id11 | Male | Programmer | 15.96 |
| id12 | Female | RiskAnalyst | 15.9 |

The assignment is to, using the data in Table [4-1](#469054_1_En_4_Chapter.xhtml#Tab1), calculate the swimming speed for each swimmer and add it as a new column to the DataFrame, as shown in Table [4-2](#469054_1_En_4_Chapter.xhtml#Tab2).

Table 4-2

Swimming Speed Column Added

| id | Gender | Occupation | swimTimeInSecond | swimmerSpeed |
| --- | --- | --- | --- | --- |
| id1 | Male | Programmer | 16.73 | 1.195 |
| id2 | Female | Manager | 15.56 | 1.285 |
| id3 | Male | Manager | 15.15 | 1.32 |
| id4 | Male | RiskAnalyst | 15.27 | 1.31 |
| id5 | Male | Programmer | 15.65 | 1.278 |
| id6 | Male | RiskAnalyst | 15.74 | 1.271 |
| id7 | Female | Programmer | 16.8 | 1.19 |
| id8 | Male | Manager | 17.11 | 1.169 |
| id9 | Female | Programmer | 16.83 | 1.188 |
| id10 | Female | RiskAnalyst | 16.34 | 1.224 |
| id11 | Male | Programmer | 15.96 | 1.253 |
| id12 | Female | RiskAnalyst | 15.9 | 1.258 |

If we want to do an operation on each element of a column in the DataFrame, we have to use the `withColumn()` function.

The `withColumn()` function is defined on the DataFrame object. This function can add a new column to the existing DataFrame or it can replace a column with a new column containing new data. We have to use some expression to get the data of the new column.

### How It Works

#### Step 4-1-1\. Creating a DataFrame

The first step is the simplest one. As we discovered in the previous chapter, we can read a CSV file and create a DataFrame from it.

```
In [1]: swimmerDf = spark.read.csv('swimmerData.csv',
header=True, inferSchema=True)
In [2]: swimmerDf.show(4)
+----+------+-----------+----------------+
|  id|Gender| Occupation|swimTimeInSecond|
+----+------+-----------+----------------+
| id1|  Male| Programmer|           16.73|
| id2|Female|    Manager|           15.56|
| id3|  Male|    Manager|           15.15|
| id4|  Male|RiskAnalyst|           15.27|
+----+------+-----------+----------------+
only showing top 4 rows
```

The `swimmerDf` DataFrame has been created. We used the `inferSchema` argument set to `True`. It means that PySpark SQL will infer the schema on its own. Therefore, it is better to check it. So let’s print it using the `printSchema()` function .

```
In [3]:  swimmerDf.printSchema()
```

Here is the output:

```
root
|-- id: string (nullable = true)
|-- Gender: string (nullable = true)
|-- Occupation: string (nullable = true)
|-- swimTimeInSecond: double (nullable = true)
```

#### Step 4-1-2\. Calculating Swimmer Speed for Each Swimmer and Adding It as a Column

How do we calculate the swimmer speed? Speed is defined as distance over time, which we can calculate using the `withColumn()` function as an expression. We have to calculate the speed of each swimmer and add the results as a new column. The following line of code serves this purpose.

```
In [4]: swimmerDf1 = swimmerDf.withColumn('swimmerSpeed',20.0/swimmerDf.swimTimeInSecond)
```

We know that the `withColumn()` function returns a new DataFrame. The new column will be added to the newly created DataFrame called `swimmerDf1`. We can verify this using the `show()` function.

```
In [5]: swimmerDf1.show(4)
```

Here is the output:

```
+---+------+-----------+----------------+------------------+
| id|Gender| Occupation|swimTimeInSecond|      swimmerSpeed|
+---+------+-----------+----------------+------------------+
|id1|  Male| Programmer|           16.73| 1.195457262402869|
|id2|Female|    Manager|           15.56|1.2853470437017995|
|id3|  Male|    Manager|           15.15|1.3201320132013201|
|id4|  Male|RiskAnalyst|           15.27| 1.309757694826457|
+---+------+-----------+----------------+------------------+
only showing top 4 rows
```

In the `swimmerDf1` DataFrame, we can see that `swimmerSpeed` is the last column. If we want to round the values in the `swimmerSpeed` column, we can use the `round()` function , which is in the `pyspark.sql.functions` submodule. To work with it, we have to import it. The following code line imports the `round()` function from the `pyspark.sql.functions` submodule.

```
In [6]: from pyspark.sql.functions import round
```

#### Step 4-1-3\. Rounding the Values of the swimmerSpeed Column

The `round()` function can be used with the `withColumn()` function. We are going to use the `col` argument of the `withColumn()` function. The first argument of the `round()` function is the `swimmerSpeed` column and the second argument is `scale`. The `scale` argument defines the format of a decimal number, which is how many digits we need after the decimal point. We are setting the `scale` to 3.

```
In [7]: swimmerDf2 = swimmerDf1.withColumn('swimmerSpeed', round(swimmerDf1.swimmerSpeed, 3))
```

Now the final output is as follows:

```
In [8]: swimmerDf2.show(5)
```

Here is the output:

```
+---+------+-----------+----------------+------------+
| id|Gender| Occupation|swimTimeInSecond|swimmerSpeed|
+---+------+-----------+----------------+------------+
|id1|  Male| Programmer|           16.73|       1.195|
|id2|Female|    Manager|           15.56|       1.285|
|id3|  Male|    Manager|           15.15|        1.32|
|id4|  Male|RiskAnalyst|           15.27|        1.31|
|id5|  Male| Programmer|           15.65|       1.278|
+---+------+-----------+----------------+------------+
only showing top 5 rows
```

We have our required result.

## Recipe 4-2\. Select Columns from a DataFrame

### Problem

You want to select one or more columns from a DataFrame.

### Solution

We are going to use the `swimmerDf2` DataFrame that we created in Recipe 4-1\. Table [4-3](#469054_1_En_4_Chapter.xhtml#Tab3) shows this DataFrame.

Table 4-3

The Dataframe

| id | Gender | Occupation | swimTimeInSecond | swimmerSpeed |
| --- | --- | --- | --- | --- |
| id1 | Male | Programmer | 16.73 | 1.195 |
| id2 | Female | Manager | 15.56 | 1.285 |
| id3 | Male | Manager | 15.15 | 1.32 |
| id4 | Male | RiskAnalyst | 15.27 | 1.31 |
| id5 | Male | Programmer | 15.65 | 1.278 |
| id6 | Male | RiskAnalyst | 15.74 | 1.271 |
| id7 | Female | Programmer | 16.8 | 1.19 |
| id8 | Male | Manager | 17.11 | 1.169 |
| id9 | Female | Programmer | 16.83 | 1.188 |
| id10 | Female | RiskAnalyst | 16.34 | 1.224 |
| id11 | Male | Programmer | 15.96 | 1.253 |
| id12 | Female | RiskAnalyst | 15.9 | 1.258 |

We have to perform the following:

*   Select the `swimTimeInSecond` column.

*   Select the `id` and `swimmerSpeed` columns.

Which DataFrame API is going to help us? Here, the `select()` function will be working for us. This `select()` function is similar to the `SELECT` clause in SQL.

The `select()` function is defined in the DataFrame class. An argument of the `select()` function is columns, which we might want to select.

### How It Works

#### Step 4-2-1\. Selecting the swimTimeInSecond Column

The `select()` function can take a variable number of arguments. Its variable number of arguments will be the different columns we want to select. Let’s select the `swimTimeInSecond` column .

```
In [1]: swimmerDf3 = swimmerDf2.select("swimTimeInSecond")
In [2]: swimmerDf3.show(6)
```

Here is the output:

```
+----------------+
|swimTimeInSecond|
+----------------+
|           16.73|
|           15.56|
|           15.15|
|           15.27|
|           15.65|
|           15.74|
+----------------+
only showing top 6 rows
```

The output is shown in the `swimmerDf3` DataFrame.

#### Step 4-2-2\. Selecting the id and swimmerSpeed Columns

Now it is time to select the `id` and `swimmerSpeed` columns. The following line of code will perform the same.

```
In [3]: swimmerDf4 = swimmerDf2.select("id","swimmerSpeed")
In [4]: swimmerDf4.show(6)
```

Here is the output:

```
+---+------------+
| id|swimmerSpeed|
+---+------------+
|id1|       1.195|
|id2|       1.285|
|id3|        1.32|
|id4|        1.31|
|id5|       1.278|
|id6|       1.271|
+---+------------+
only showing top 6 rows
```

The `swimmerDf4` DataFrame shows our required result.

The `select()` function takes an expression on the column. What is an expression in the context of programming? An expression is a statement that results in either a mathematical or logical value. In the following code line, we are going to select columns such that in the new DataFrame called `swimmerDf5`, the first column will be `id` and the second column will be the `swimmerSpeed` multiplied by 2.

```
In [5]: swimmerDf5 = swimmerDf2.select("id",swimmerDf2.swimmerSpeed*2)
```

In the previous code line, `swimmerDf2.swimmerSpeed*2` is an expression.

```
In [6]: swimmerDf5.show(6)
```

Here is the output:

```
+---+------------------+
| id|(swimmerSpeed * 2)|
+---+------------------+
|id1|              2.39|
|id2|              2.57|
|id3|              2.64|
|id4|              2.62|
|id5|             2.556|
|id6|             2.542|
+---+------------------+
only showing top 6 rows
```

The final output is on display.

## Recipe 4-3\. Filter Rows from a DataFrame

### Problem

You want to apply filtering to a DataFrame.

### Solution

The filtering process is used to remove unwanted data from a dataset. It is the process of getting a required data subset from a dataset based on some condition. Datasets often come with unwanted records, therefore filtering is inevitable.

We are going to use the `swimmerDf2` DataFrame and solve the following:

*   Select records where the `Gender` column has a value of `Male`.

*   Select records where `Gender` is `Male` and `Occupation` is `Programmer`.

*   Select records where `Occupation` is `Programmer` and `swimmerSpeed > 1.17`.

We are going to use the `filter()` function. This function filters records using a given condition. We provide the filtering condition as an argument to the `filter()` function. It returns a new DataFrame. The `where()` function is an alias of the `filter()` function. If two functions are aliases of each other, we can use them interchangeably with the same argument and get the same result.

### How It Works

#### Step 4-3-1\. Selecting Records Where the Gender Column Is Male

The argument of the `filter` function is a condition to show equality. We can show equality using `swimmerDf2.Gender == 'Male'`.

```
In [1]: swimmerDf3 = swimmerDf2.filter(swimmerDf2.Gender == 'Male')
In [2]: swimmerDf3.show()
```

Here is the output:

```
+----+------+-----------+----------------+------------+
|  id|Gender| Occupation|swimTimeInSecond|swimmerSpeed|
+----+------+-----------+----------------+------------+
| id1|  Male| Programmer|           16.73|       1.195|
| id3|  Male|    Manager|           15.15|       1.320|
| id4|  Male|RiskAnalyst|           15.27|       1.310|
| id5|  Male| Programmer|           15.65|       1.278|
| id6|  Male|RiskAnalyst|           15.74|       1.271|
| id8|  Male|    Manager|           17.11|       1.169|
|id11|  Male| Programmer|           15.96|       1.253|
+----+------+-----------+----------------+------------+
```

#### Step 4-3-2\. Selecting Records Where Gender Is Male and Occupation Is Programmer

We can provide compound logical expressions in the `filter()` function. But core Python `and` and `or` operators will not work. We have to provide the `&` operator for `and` and use the `|` operator for `or`.

```
In [3]: swimmerDf4 = swimmerDf2.filter((swimmerDf2.Gender == 'Male') & (swimmerDf2.Occupation == 'Programmer')).show()
In [4]: swimmerDf4.show()
```

Here is the output:

```
+----+------+-----------+----------------+------------+
|  id|Gender| Occupation|swimTimeInSecond|swimmerSpeed|
+----+------+-----------+----------------+------------+
| id1|  Male| Programmer|           16.73|       1.195|
| id5|  Male| Programmer|           15.65|       1.278|
|id11|  Male| Programmer|           15.96|       1.253|
+----+------+-----------+----------------+------------+
```

The `swimmerDf4` DataFrame consists of all records belonging to male swimmers only.

#### Step 4-3-3\. Selecting Records Where Occupation Is Programmer and swimmerSpeed > 1.17

In the last assignment of this recipe, we have to find the records of programmers where their `swimmerSpeed` value is greater than 1.17 m/s.

```
In [5]: swimmerDf2.filter((swimmerDf2.Occupation == 'Programmer') & (swimmerDf2.swimmerSpeed > 1.17) ).show()
In [6]: swimmerDf5.show()
```

Here is the output:

```
+----+------+----------+----------------+------------+
|  id|Gender|Occupation|swimTimeInSecond|swimmerSpeed|
+----+------+----------+----------------+------------+
| id1|  Male|Programmer|           16.73|       1.195|
| id5|  Male|Programmer|           15.65|       1.278|
| id7|Female|Programmer|            16.8|        1.19|
| id9|Female|Programmer|           16.83|       1.188|
|id11|  Male|Programmer|           15.96|       1.253|
+----+------+----------+----------------+------------+
```

## Recipe 4-4\. Delete a Column from an Existing DataFrame

### Problem

You want to delete some columns from a DataFrame.

### Solution

Data scientists can get structured datasets in which some columns are redundant and must be removed before analysis.

In this recipe, you want to accomplish the following:

*   Drop the `id` column from the `swimmerDf2` DataFrame.

*   Drop the `id` and `Occupation` columns from `swimmerDf2`.

The `drop()` function can be used to drop one or more columns from a DataFrame. It takes columns to be dropped as its argument. It returns a new DataFrame. The new DataFrame will not contain the dropped columns.

### How It Works

#### Step 4-4-1\. Dropping the id Column from the swimmerDf2 DataFrame

We start by dropping the `id` column :

```
In [1]: swimmerDf3 = swimmerDf2.drop(swimmerDf2.id)
In [2]: swimmerDf3.show(6)
```

Here is the output:

```
+------+-----------+----------------+------------+
|Gender| Occupation|swimTimeInSecond|swimmerSpeed|
+------+-----------+----------------+------------+
|  Male| Programmer|           16.73|       1.195|
|Female|    Manager|           15.56|       1.285|
|  Male|    Manager|           15.15|        1.32|
|  Male|RiskAnalyst|           15.27|        1.31|
|  Male| Programmer|           15.65|       1.278|
|  Male|RiskAnalyst|           15.74|       1.271|
+------+-----------+----------------+------------+
only showing top 6 rows
```

We can get this same result by passing `id` as an argument.

```
In [3]: swimmerDf4 = swimmerDf2.drop("id")
In [4]: swimmerDf4.show(6)
```

Here is the output:

```
+------+-----------+----------------+------------+
|Gender| Occupation|swimTimeInSecond|swimmerSpeed|
+------+-----------+----------------+------------+
|  Male| Programmer|           16.73|       1.195|
|Female|    Manager|           15.56|       1.285|
|  Male|    Manager|           15.15|        1.32|
|  Male|RiskAnalyst|           15.27|        1.31|
|  Male| Programmer|           15.65|       1.278|
|  Male|RiskAnalyst|           15.74|       1.271|
+------+-----------+----------------+------------+
only showing top 6 rows
```

#### Step 4-4-2\. Dropping the id and Occupation Columns from swimmerDf2

Deleting or dropping more than one column is very easy. We have to provide all the columns as strings and the number of arguments as variables to the `drop()` function:

```
In [5]: swimmerDf5 = swimmerDf2.drop("id", "Occupation")
In [6]: swimmerDf5.show(6)
```

Here is the output:

```
+------+----------------+------------+
|Gender|swimTimeInSecond|swimmerSpeed|
+------+----------------+------------+
|  Male|           16.73|       1.195|
|Female|           15.56|       1.285|
|  Male|           15.15|       1.320|
|  Male|           15.27|       1.310|
|  Male|           15.65|       1.278|
|  Male|           15.74|       1.271|
+------+----------------+------------+
only showing top 6 rows
```

We have our final result in the `swimmerDf5` DataFrame .

## Recipe 4-5\. Create and Use a PySpark SQL UDF

### Problem

You want to create a user-defined function (UDF) and apply it to a DataFrame column.

### Solution

You might be thinking that everyone who knows Python can create a user-defined function easily. What is so special about them? But, here in PySpark SQL, UDFs work on a column. A UDF works on each element of a column, which results in a new column.

Figure [4-1](#469054_1_En_4_Chapter.xhtml#Fig1) shows the average temperature collected in Celsius over seven days and how we can add a column that translates the temperatures to degrees Fahrenheit.

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig1_HTML.jpg](images/469054_1_En_4_Chapter/469054_1_En_4_Fig1_HTML.jpg)

Figure 4-1

Adding a Fahrenheit column

As we can see in Figure [4-1](#469054_1_En_4_Chapter.xhtml#Fig1), the table on the left has `day` as the first column and temperature in Celsius as the second column. For us, this table is a DataFrame. We have to create a new DataFrame, which is shown on the right side of Figure [4-1](#469054_1_En_4_Chapter.xhtml#Fig1). This new DataFrame has a third column, which lists the temperature in Fahrenheit. For each value in column two, we are transforming that value into degrees Fahrenheit in column three.

It seems a very simple problem. We first create a Python function that will take a temperature value in Celsius and return that temperature in Fahrenheit. You might wonder if there is an existing function to perform this task. How do we apply the created function to each value in the column? Are we going to use some sort of loop? No, we just have to make this function a UDF and the rest will be taken care by the PySpark SQL API’s function `withColumn()`.

Transforming a simple Python function into a UDF is very simple. We are going to use the `udf()` function, as follows:

```
udf(f= None, returnType =StringType)
```

The `udf()` function is defined in the PySpark submodule `pyspark.sql.functions` . It takes two arguments. The first argument is a Python function and the second argument is the return datatype of this function. The return datatype will be from the PySpark submodule `pyspark.sql.types`. The default value of the `returnType` argument is `StringType`.

We are going to solve the given problem in a step-by-step fashion.

### How It Works

#### Step 4-5-1\. Creating a DataFrame

We have been given data in a Parquet file. The name of the data directory is `temperatureData` . We need the `DoubleType` class and the `udf` function. Therefore, first we are going to import the required function and class.

```
In [1]: from pyspark.sql.types import DoubleType
from pyspark.sql.functions import udf
```

From Spark 2.0.0 and onward, it is very easy to read data from different sources, which we have discussed in detail in previous chapters. In order to read data from a Parquet file, we have to use the `spark.read.parquet()` function , where the `spark` is an object of the `SparkSession` class. This `spark` object is provided by the console.

```
In [2]: tempDf = spark.read.parquet('temperatureData')
In [3]: tempDf.show(6)
```

Here is the output:

```
+----+-------------+
| day|tempInCelsius|
+----+-------------+
|day1|         12.2|
|day2|         13.1|
|day3|         12.9|
|day4|         11.9|
|day5|         14.0|
|day6|         13.9|
+----+-------------+
only showing top 6 rows
```

#### Step 4-5-2\. Creating a UDF

Creating a PySpark SQL UDF is in general a two-step process. First we have to create a Python function for the purpose and then we have to transform the created Python function to a UDF function using the `udf()` function.

To transform the Celsius values into Fahrenheit, we create a Python function called `celsiustoFahrenheit` . This function has one argument called `temp`, which is temperature in Celsius.

```
In [4]: def celsiustoFahrenheit(temp):
...:     return ((temp*9.0/5.0)+32)
...:
```

Let’s test the working of our Python function.

```
In [5]: celsiustoFahrenheit(12.2)
Out[5]: 53.96
```

The test result shows that the `celsiustoFahrenheit` function is working as expected. Now let’s transform our Python function to a UDF.

```
In [6]: celsiustoFahrenheitUdf = udf("celsiustoFahrenheit", DoubleType())
```

We can observe that the `udf()` function has taken the name of the function in `String` format as its first argument and the return type of the UDF as the second argument. The return type of our UDF is `DoubleType`. Here, `celsiustoFahrenheitUdf` is our required UDF. It will be applied to each value in the `tempInCelsius` column and return the temperature in degrees Fahrenheit.

#### Step 4-5-3\. Using the UDF to Create a New Column

The required UDF has been created. So, we’ll now use this UDF to transform the temperature from Celsius to Fahrenheit and add the result as a new column. We are going to use the `withColumn()` function with a second argument as a UDF and with `tempInCelsius` as the input to the UDF.

```
In [7]: tempDfFahrenheit = tempDf.withColumn('tempInFahrenheit', celsiustoFahrenheitUdf(tempDf.tempInCelsius))
In [7]: tempDfFahrenheit.show(6)
```

Here is the output:

```
+----+-------------+----------------+
| day|tempInCelsius|tempInFahrenheit|
+----+-------------+----------------+
|day1|         12.2|           53.96|
|day2|         13.1|           55.58|
|day3|         12.9|           55.22|
|day4|         11.9|           53.42|
|day5|         14.0|           57.20|
|day6|         13.9|           57.02|
+----+-------------+----------------+
only showing top 6 rows
```

The `tempDfFahrenheit` output shows that we have completed the recipe successfully.

#### Step 4-5-4\. Saving the Resultant DataFrame as a CSV File

It’s a good idea to save the results for further use.

```
In [8]: tempDfFahrenheit.write.csv(path='tempInCelsAndFahren',header=True,sep=',')
$ cd tempInCelsAndFahren
$ ls
```

Here is the output:

```
part-00000-1320146a-7998-4f3b-9bdd-939227c793c9-c000.csv  _SUCCESS
```

The result is in the `part-00000-1320146a-7998-4f3b-9bdd-939227c793c9-c000.csv` file.

## Recipe 4-6\. Data Labeling

### Problem

You want to label the data points in a DataFrame column.

### Solution

We have dealt with temperature data in Recipe 4-5\. See Figure [4-2](#469054_1_En_4_Chapter.xhtml#Fig2).

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig2_HTML.jpg](images/469054_1_En_4_Chapter/469054_1_En_4_Fig2_HTML.jpg)

Figure 4-2

Labeling the data

Figure [4-2](#469054_1_En_4_Chapter.xhtml#Fig2) displays our job for this recipe. We have to label the data in the `tempInCelsius` column. We have to create a new column named `label`. This label will report `High` if the corresponding `tempInCelsius` value is greater than 12.9 and `Low` otherwise.

### How It Works

#### Step 4-6-1\. Creating the UDF to Create a Label

We know that we need a `udf()` function because we have to create a UDF that can create labels .

```
In [1]: from pyspark.sql.functions import udf
```

We are going to create a Python function named `labelTemprature`. This function will take the temperature in Celsius as the input and return `High` or `Low`, depending on the conditions.

```
In [2]: def labelTemprature(temp) :
...:     if temp > 12.9 :
...:         return "High"
...:     else :
...:         return "Low"
In [3]: labelTemprature(11.99)
Out[3]: 'Low'
In [4]: labelTemprature(13.2)
Out[4]: 'High'
```

Let’s create a PySpark SQL UDF using `labelTemprature`.

```
In [5]: labelTempratureUdf = udf(labelTemprature)
```

#### Step 4-6-2\. Creating a New DataFrame with a New Label Column

The new DataFrame called `tempDf2` is created with a new column label using the `withColumn()` function :

```
In [6]: tempDf2 = tempDf.withColumn("label", labelTempratureUdf(tempDf.tempInCelsius))
In [7]: tempDf2.show()
```

Here is the output:

```
+----+-------------+-----+
| day|tempInCelsius|label|
+----+-------------+-----+
|day1|         12.2|  Low|
|day2|         13.1| High|
|day3|         12.9|  Low|
|day4|         11.9|  Low|
|day5|         14.0| High|
|day6|         13.9| High|
|day7|         12.7|  Low|
+----+-------------+-----+
```

We have labeled the data.

## Recipe 4-7\. Perform Descriptive Statistics on a Column of a DataFrame

### Problem

You want to calculate descriptive statistics measures on columns in a DataFrame.

### Solution

Descriptive statistics provide you with important information about your data. The important descriptive statistics are count, sum, mean, sample variance, and sample standard deviation. Let’s discuss them one by one.

Consider the data points x1, x2, . . ,xn from the variable `x`. Figure [4-3](#469054_1_En_4_Chapter.xhtml#Fig3) shows the mathematical formula for how to calculate a sample mean. The sample mean is a measure of the central tendency of datasets.

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig3_HTML.png](images/469054_1_En_4_Chapter/469054_1_En_4_Fig3_HTML.png)

Figure 4-3

Calculating the mean

Variance is a measure of the spread of a dataset. Figure [4-4](#469054_1_En_4_Chapter.xhtml#Fig4) shows the mathematical formula for calculating population variance.

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig4_HTML.png](images/469054_1_En_4_Chapter/469054_1_En_4_Fig4_HTML.png)

Figure 4-4

Calculating population variance

Figure [4-5](#469054_1_En_4_Chapter.xhtml#Fig5) portrays the mathematical formula to calculate sample variance.

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig5_HTML.png](images/469054_1_En_4_Chapter/469054_1_En_4_Fig5_HTML.png)

Figure 4-5

Sample variance

We have been given a JSON data file called `corrData.json` . The contents of the file are as follows.

```
{"iv1":5.5,"iv2":8.5,"iv3":9.5}
{"iv1":6.13,"iv2":9.13,"iv3":10.13}
{"iv1":5.92,"iv2":8.92,"iv3":9.92}
{"iv1":6.89,"iv2":9.89,"iv3":10.89}
{"iv1":6.12,"iv2":9.12,"iv3":10.12}
```

Imagine this data in tabular form. We can see that the data has three columns—`iv1`, `iv2`, and `iv3`. Each column has decimal or floating point values.

All data descriptive measures—like mean, sum and other functions—are found in the `pyspark.sql.functions` submodule .

*   `avg()`: Calculates the mean of a column. We can also use the `mean()` function in place of `avg()`.

*   `max()`: Finds the maximum value for a given column.

*   `Mmn()`: Finds the minimum value in a given column.

*   `sum()`: Performs summation on the values of a column.

*   `count()`: Counts the number of elements in a column.

*   `var_samp()`: Calculates sample variance. We can use the `variance()` function in place of the `var_samp()` function.

*   `var_pop()`: If you want to calculate population variance, the `var_pop()` function will be used.

*   `stddev_samp()`: The sample standard deviation can be calculated using the `stddev()` or `stddev_samp()` function.

*   `stddev_pop()`: Calculates the population standard deviation.

Many more can be found in the `pyspark.sql.functions` submodule .

We are going to execute the following:

*   Mean of each column.

*   Variance of each column.

*   Total number of data points in each column.

*   Summation, mean, and standard deviation of the first column.

*   Variance of the first column, mean of the second column, and standard deviation of the third column.

To apply aggregation on columns of a DataFrame, we are going to use the `agg()` function , which is defined on a DataFrame and returns a DataFrame. The input of `agg()` will be an expression. The input is a dictionary format, where the key of each element will be the column name and the value will be the aggregation operation we want to perform on that column. We will discuss this in more detail further.

### How It Works

#### Step 4-7-1\. Reading the Data and Creating a DataFrame

The data is in a JSON file. We are going to read it using the `spark.read.json` function .

```
In [1]: corrData = spark.read.json(path='corrData.json')
In [2]: corrData.show(6)
```

Here is the output:

```
+----+----+-----+
| iv1| iv2|  iv3|
+----+----+-----+
| 5.5| 8.5|  9.5|
|6.13|9.13|10.13|
|5.92|8.92| 9.92|
|6.89|9.89|10.89|
|6.12|9.12|10.12|
|6.32|9.32|10.32|
+----+----+-----+
only showing top 6 rows
```

We have created the DataFrame successfully. Whenever we do not provide the schema of the DataFrame explicitly, it is better to check the schema of a newly created DataFrame, to ensure that everything is as expected.

```
In [3]: corrData.printSchema()
```

Here is the output:

```
root
|-- iv1: double (nullable = true)
|-- iv2: double (nullable = true)
|-- iv3: double (nullable = true)
```

And everything is as expected.

#### Step 4-7-2\. Calculating the Mean of Each Column

As we discussed, we are going to use the `agg()` function to execute aggregation on the DataFrame columns. This ag`g`() function is going to take a dictionary as its input. The key will be the column name and the values will be the aggregation we want to execute. Both of these will be provided as `String`. We have to calculate the average value on each column. So the required input to the `agg()` function will be `{"iv1":"avg","iv2":"avg","iv3":"avg"}`, which is a Python dictionary. Each key of the dictionary is a column name in DataFrame and each value is associated with the aggregation function that we want to calculate for the column name as key.

```
In [3]: meanVal = corrData.agg({"iv1":"avg","iv2":"avg","iv3":"avg"})
In [4]: meanVal.show()
```

Here is the output:

```
+-----------------+-----------------+------------------+
|         avg(iv2)|         avg(iv1)|          avg(iv3)|
+-----------------+-----------------+------------------+
|9.044666666666666|6.044666666666666|10.044666666666668|
+-----------------+-----------------+------------------+
```

We have calculated the mean of each column.

#### Step 4-7-3\. Calculating the Variance of Each Column

Now we have to calculate the variance of each column. We know that there are two types of variance—sample variance and population variance. We are going to calculate each and you can use them according to your needs. Let’s start with sample variance.

```
In [5]: varSampleVal = corrData.agg({"iv1":"var_samp","iv2":"var_samp","iv3":"var_samp"})
In [6]: varSampleVal.show()
```

Here is the output:

```
+-------------------+-------------------+-------------------+
|      var_samp(iv2)|      var_samp(iv1)|      var_samp(iv3)|
+-------------------+-------------------+-------------------+
|0.24509809523809528|0.24509809523809528|0.24509809523809528|
+-------------------+-------------------+-------------------+
```

Now we calculate the population variance:

```
In [7]: varPopulation = corrData.agg({"iv1":"var_pop","iv2":"var_pop","iv3":"var_pop"})
In [8]: varPopulation.show()
```

Here is the output:

```
+---------------+---------------+---------------+
|   var_pop(iv2)|   var_pop(iv1)|   var_pop(iv3)|
+---------------+---------------+---------------+
|0.2287582222222|0.2287582222222|0.2287582222222|
+---------------+---------------+---------------+
```

#### Step 4-7-4\. Counting the Number of Data Points in Each Column

Now we know how to apply aggregation on different columns. But to be confident with this process, let’s apply one more aggregation, which counts the number of elements in each column.

```
In [10]: countVal = corrData.agg({"iv1":"count","iv2":"count","iv3":"count"})
In [11]: countVal.show()
```

Here is the output:

```
+----------+----------+----------+
|count(iv2)|count(iv1)|count(iv3)|
+----------+----------+----------+
|        15|        15|        15|
+----------+----------+----------+
```

#### Step 4-7-5\. Calculating Summation, Mean, and Standard Deviation on the First Column

Sometimes you have to apply many aggregations on the same column. Let’s apply summation, average, and a sample standard deviation on column `iv1` as an example.

```
In [12]: moreAggOnOneCol = corrData.agg({"iv1":"sum","iv1":"avg","iv1":"stddev_samp"})
In [13]: moreAggOnOneCol.show()
```

Here is the output:

```
+-------------------+
|   stddev_samp(iv1)|
+-------------------+
|0.49507382806819356|
+-------------------+
```

Here the output is not as we expect. What is the reason behind this failure? It’s due to the Python dictionary. The dictionary key must be unique, otherwise the last value overwrites the other values.

What is the solution? We can provide the aggregation function as multiple arguments one by one. In order to apply the aggregation function directly as a function, we have to first import all. Recall that all the aggregation functions are found in the `pyspark.sql.functions` submodule.

```
In [14]: from pyspark.sql.functions import *
In [15]: moreAggOnOneCol = corrData.agg(sum("iv1"), avg("iv1"), stddev_samp("iv1"))
In [16]: moreAggOnOneCol.show()
```

Here is the output:

```
+-------------+-------------+----------------+
|     sum(iv1)|     avg(iv1)|stddev_samp(iv1)|
+-------------+-------------+----------------+
|90.6699999999|6.04466666666| 0.4950738280681|
+-------------+-------------+----------------+
```

Now we get the expected result.

#### Step 4-7-6\. Calculating the Variance of the First Column, the Mean of the Second Column, and the Standard Deviation of the Third Column

Can we apply different aggregations on different columns in one go? Yes we can. The following code shows how to do just that.

```
In [24]: colWiseDiffAggregation = corrData.agg({"iv1":"var_samp","iv2":"avg","iv3":"stddev_samp"})
In [25]: colWiseDiffAggregation.show()
```

Here is the output:

```
+-------------+---------------+----------------+
|     avg(iv2)|  var_samp(iv1)|stddev_samp(iv3)|
+-------------+---------------+----------------+
|9.04466666666|0.2450980952380| 0.4950738280681|
+-------------+---------------+----------------+
```

## Recipe 4-8\. Calculate Covariance

### Problem

You want to calculate the covariance between two columns of a DataFrame.

### Solution

Covariance shows the relationship between two variables. It shows the linear change in one variable based on another.

Say we have the data points x[1,] x[2], . . ,x[n] from the variable `x` and y[1], y[2], . . ,y[n] from variable `y`. μ[x] and μ[y] are the mean values of the `x` and `y` variables, respectively. Figure [4-6](#469054_1_En_4_Chapter.xhtml#Fig6) is the mathematical formula that represents how to calculate sample covariance.

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig6_HTML.png](images/469054_1_En_4_Chapter/469054_1_En_4_Fig6_HTML.png)

Figure 4-6

Sample covariance

In PySpark SQL, we can calculate sample covariance using the `cov(col1, col2)` API. This function takes two columns of the DataFrame at a time. The `cov()` function under the `DataFrame` and `DataFrameStatFunctions` classes are aliases of each other.

We have to find the following:

*   Covariance between variable `iv1` and `iv2`.

*   Covariance between variable `iv3` and `iv1`.

*   Covariance between variable `iv2` and `iv3`.

### How It Works

We are going to use the `corrData` DataFrame created in Recipe 4-7.

#### Step 4-8-1\. Calculating Covariance Between Variables iv1 and iv2

We are going to calculate covariance between column `iv1` and column `iv2`.

```
In [48]: corrData.cov('iv1','iv2')
```

Here is the output:

```
Out[48]: 0.24509809523809525
```

#### Step 4-8-2\. Calculating Covariance Between Variables iv3 and iv1

It is time to calculate the covariance between column `iv1` and column iv3.

```
In [49]: corrData.cov('iv1','iv3')
```

Here is the output:

```
Out[49]: 0.24509809523809525
```

#### Step 4-8-3\. Calculating Covariance Between Variables iv2 and iv3

Finally, we calculate the covariance between columns `iv2` and `iv3`.

```
In [50]: corrData.cov('iv2','iv3')
```

Here is the output:

```
Out[50]: 0.2450980952380953
```

## Recipe 4-9\. Calculate Correlation

### Problem

You want to calculate the correlation between two columns of a DataFrame.

### Solution

The correlation shows the relationship between two variables. It shows how a change to one variable affects another. It is normalized covariance. The value of covariance can take any positive value, therefore, we normalize correlation to properly interpret the relationship of two variables. The correlation value lies in the range of -1 to 1, inclusive.

Say we have the data points x[1], x[2], . . ,x[n] from variable `x` and y[1], y[2], . . ,y[n] from the variable `y`. μx and μy are the mean values of the `x` and `y` variables, respectively. The mathematical formula in Figure [4-7](#469054_1_En_4_Chapter.xhtml#Fig7) shows how to calculate correlation with this data.

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig7_HTML.png](images/469054_1_En_4_Chapter/469054_1_En_4_Fig7_HTML.png)

Figure 4-7

Correlation

In PySpark SQL, we can calculate correlation using `cov(col1, col2)`. This function takes two columns of the DataFrame at a time. The `cov()` function under the `DataFrame` and `DataFrameStatFunctions` classes are aliases of each other.

We have to find the following:

*   Correlation between `iv1` and `iv2`.

*   Correlation between `iv3` and `iv1`.

*   Correlation between `iv2` and `iv3`.

### How It Works

We are going to use the `corrData` DataFrame created in Recipe 4-7.

#### Step 4-9-1\. Calculating Correlation Between Variables iv1 and iv2

Here is how we calculate the correlation between columns `iv1` and `iv2`.

```
In [10]: corrData.corr('iv1','iv2')
Out[10]: 0.9999999999999998
```

Similarly, the correlation between other columns can be calculated in the following steps.

#### Step 4-9-2\. Calculating Correlation Between Variables iv3 and iv1

```
In [11]: corrData.corr('iv1','iv3')
Out[11]: 0.9999999999999998
```

#### Step 4-9-3\. Calculating Correlation Between Variables iv2 and iv3

```
In [12]: corrData.corr('iv2','iv3')
Out[12]: 1.0
```

## Recipe 4-10\. Describe a DataFrame

### Problem

You want to calculate the summary statistics on all the columns in a DataFrame.

### Solution

In Recipe 4-7, we learned how to calculate different summary statistics using DataFrame’s built-in aggregation functions.

PySpark SQL has provided two more very robust and easy-to-use functions, which calculate a group of summary statistics for each column. These functions are called `describe()` and `summary()`.

The `describe()` function , which is defined on a DataFrame, calculates min, max, count, mean, and stddev for each column. For columns with categorical values, the `describe()` function returns count, min, and max for each categorical column. This function is used for exploratory data analysis. We can apply the `describe()` function on a single column or on columns. If no input is given, this function applies the summary statistics on each column and returns the result as a new DataFrame.

The `summary()` function calculates the summary statistics min, max, count, mean, and stddev, which is similar to the `describe(`) function. Apart from this, the `summary()` function calculates the median and the 25 and 50 percentiles.

The `describe()` function takes columns as its input. It calculates specified summary statistics for each input. On the other hand, the `summary()` function takes the summary statistic names as Python strings and returns the summary statistics on each column that we provided as an argument.

We are going to use the `corrData` DataFrame we created in Recipe 4-7\. We want to perform the following:

*   Apply the `describe()` function on each column.

*   Apply the `describe()` function on columns `iv1` and `iv2`.

*   Add a column of a categorical variable to the `corrData` DataFrame and apply the `describe()` function on the categorical column.

*   Apply the `summary()` function on each column.

*   Apply the `summary(`) function on columns `iv2` and `iv3`.

### How It Works

#### Step 4-10-1\. Applying the describe( ) Function on Each Column

We are going to apply the `describe()` function on each column of the DataFrame. If there is no input to the `describe` function, it will be applied on every column.

```
In [1]: dataDescription =  corrData.describe()
In [2]: dataDescription.show()
```

Here is the output:

```
+-------+--------+---------+--------+
|summary|     iv1|      iv2|     iv3|
+-------+--------+---------+--------+
|  count|      15|       15|      15|
|   mean| 6.04466|  9.04466| 10.0446|
| stddev|0.495073|0.4950738|0.495073|
|    min|    5.17|     8.17|    9.17|
|    max|    6.89|     9.89|   10.89|
+-------+--------+---------+--------+
```

The `describe()` function returns the summary statistics. In the first column of the result, we can see all the summary statistic names. The second column, named `iv1`, provides values for the summary statistics values. The first value `15` in column `iv1` is the number of elements in column `iv1`. Similarly, the second value `6.044666` in column `iv1` is the mean value of data in that column. PySpark SQL will return a mean value with many decimal points. Some part of the mean result has been truncated, so it should be readable here.

#### Step 4-10-2\. Applying the describe( ) Function on Columns iv1 and iv2

We can apply the `describe()` function with column selection. Here, we are going to apply the `describe()` function on columns `iv1` and `iv2`.

```
In [3]: dataDescriptioniv1iv2 =  corrData.describe(['iv1', 'iv2'])
In [4]: dataDescriptioniv1iv2.show()
```

Here is the output:

```
+-------+--------+---------+
|summary|     iv1|      iv2|
+-------+--------+---------+
|  count|      15|       15|
|   mean| 6.04466|  9.04466|
| stddev|0.495073|0.4950738|
|    min|    5.17|     8.17|
|    max|    6.89|     9.89|
+-------+--------+---------+
```

Can we apply selective summary statistics like mean and variance on columns? Not using the `describe()` function. In order to provide selective summary statistics, we have to use the `summary()` function.

#### Step 4-10-3\. Applying the summary( ) Function on Each Column

As we have discussed, the `summary()` function is similar to the `describe()` function. The `summary()` function provides 25, 50, and 75 quantiles. In order to apply the `summary()` function to our summary statistics, we don’t provide any input.

```
In [5]: summaryData  = corrData.summary()
In [6]: summaryData.show()
```

Here is the output:

```
+-------+--------+---------+--------+
|summary|     iv1|      iv2|     iv3|
+-------+--------+---------+--------+
|  count|      15|       15|      15|
|   mean| 6.04466|  9.04466| 10.0446|
| stddev|0.495073|0.4950738|0.495073|
|    min|    5.17|     8.17|    9.17|
|    25%|    5.64|     8.64|    9.64|
|    50%|     6.1|      9.1|    10.1|
|    75%|    6.32|     9.32|   10.32|
|    max|    6.89|     9.89|   10.89|
+-------+--------+---------+--------+
```

Using the `summary()` function, we can apply selective summary statistics. The following line of code determines the mean and maximum value using the `summary()` function.

```
In [7]: summaryMeanMax = corrData.summary(['mean','max'])
In [8]: summaryMeanMax.show()
```

Here is the output:

```
+-------+-------+-------+-------+
|summary|    iv1|    iv2|    iv3|
+-------+-------+-------+-------+
|   mean|6.04466|9.04466|10.0446|
|    max|   6.89|   9.89|  10.89|
+-------+-------+-------+-------+
```

#### Step 4-10-4\. Applying the summary( ) Function on Columns iv2 and iv3

In order to apply the `summary()` function on selective columns, we have to first select the required columns using the `select()` function. Then, on selected columns, we can apply the `summary()` function.

```
In [9]: summaryiv1iv2 = corrData.select('iv1','iv2').summary('min','max')
```

We select columns `iv1` and `iv2` using the `select()` function and then apply the `summary()` function to calculate the minimum value and maximum value on the selected columns.

```
In [10]: summaryiv1iv2.show()
```

Here is the output:

```
+-------+----+----+
|summary| iv1| iv2|
+-------+----+----+
|    min|5.17|8.17|
|    max|6.89|9.89|
+-------+----+----+
```

#### Step 4-10-5\. Adding a Column of Categorical Variables

We can add a column of categorical variables to the `corrData` DataFrame and then apply the `describe()` function on that categorical column.

You already know how the `describe()` function can be applied on numerical data. But you might want to know the behavior of the `describe()` function on the categorical variable. Categorical values are strings. We can find the minimum and maximum value of the categorical data. Apart from minimum and maximum, we can also determine the number of elements.

Now, we are going to create a UDF called `labelIt()`. The input of the `labelIt` function will be the values in column `iv3`. The UDF is going to return `High` if the input is greater than 10.0\. Otherwise, it will return `Low`.

```
In [11]: from pyspark.sql.functions import udf
In [12]: def labelIt(x):
...:          if x > 10.0 :
...:                   return 'High'
...:                else:
...:                   return 'Low'
In [13]: labelIt = udf(labelIt)
```

We have now created the `labelIt()` UDF. The output of this UDF will constitute elements in the `iv4` column in the newly created DataFrame, called `corrData1`.

```
In [14]: corrData1 = corrData.withColumn('iv4', labelIt('iv3'))
In [15]: corrData1.show(5)
```

Here is the output:

```
+----+----+-----+----+
| iv1| iv2|  iv3| iv4|
+----+----+-----+----+
| 5.5| 8.5|  9.5| Low|
|6.13|9.13|10.13|High|
|5.92|8.92| 9.92| Low|
|6.89|9.89|10.89|High|
|6.12|9.12|10.12|High|
+----+----+-----+----+
only showing top 5 rows
```

The `corrData1` DataFrame has an extra column at the end, called `iv4`. This column has categorical values of `Low` and `High`. We are going to apply summaries on each column of the DataFrame.

```
In [16]: meanMaxSummary = corrData1.summary('mean','max')
In [17]: meanMaxSummary.show()
```

Here is the output:

```
+-------+---------+---------+--------+----+
|summary|      iv1|      iv2|     iv3| iv4|
+-------+---------+---------+--------+----+
|   mean|6.0446666|9.0446666|10.04466|null|
|    max|     6.89|     9.89|   10.89| Low|
+-------+---------+---------+--------+----+
```

Now the others:

```
In [18]:  countMinMaxSummary  = corrData1.summary('count','min','max')
In [19]:  countMinMaxSummary.show()
+-------+----+----+-----+----+
|summary| iv1| iv2|  iv3| iv4|
+-------+----+----+-----+----+
|  count|  15|  15|   15|  15|
|    min|5.17|8.17| 9.17|High|
|    max|6.89|9.89|10.89| Low|
+-------+----+----+-----+----+
```

## Recipe 4-11\. Sort Data in a DataFrame

### Problem

You want to sort records in a DataFrame.

### Solution

You need to perform sorting operations from time to time, to sort the data for yourself or when some mathematical or statistical algorithm requires that the input be in sorted order. Sorting can be applied in increasing or decreasing order relative to some key or column.

The PySpark SQL API `orderBy(*cols, **kwargs)` can be used to sort records. It returns a new DataFrame, sorted by specified columns. We specify columns as the `*cols` argument of the function. The `*` in `*cols` is for the variable number of arguments and we are going to provide different columns as strings. The second argument `**kwargs` is a key/value pair. We are going to provide ascending as the key and its associated value as a Boolean or a list of Booleans. If we provide a list of Booleans as a value of the ascending key, the number of lists must be equal to the number of columns we provided in the `cols` argument.

We are going to perform sorting operations on a DataFrame we created in Recipe 4-1, that is `swimmerDf` . Let’s look at the `Rows` of `swimmerDf` to refresh our memory.

```
In [1]: swimmerDf.show(4)
+---+------+-----------+----------------+
| id|Gender| Occupation|swimTimeInSecond|
+---+------+-----------+----------------+
|id1|  Male| Programmer|           16.73|
|id2|Female|    Manager|           15.56|
|id3|  Male|    Manager|           15.15|
|id4|  Male|RiskAnalyst|           15.27|
+---+------+-----------+----------------+
only showing top 4 rows
```

We have to perform the following:

*   Sort the `swimmerDf` DataFrame on the `swimTimeInSecond` column in ascending order.

*   Sort the `swimmerDf` DataFrame on the `swimTimeInSecond` column in descending order.

*   Sort the `swimmerDf` DataFrame on the `Occupation` and `swimTimeInSecond` columns in descending and ascending order, respectively.

### Note

We can also use the `sort()` function in place of the `orderBy()` function.

### How It Works

#### Step 4-11-1\. Sorting a DataFrame in Ascending Order

We will sort the `swimmerDf` DataFrame on the `swimTimeInSecond` column in ascending order. The default value of the `ascending` argument is `True`. So, the following line of code will do what we need.

```
In [2]: swimmerDfSorted1 = swimmerDf.orderBy("swimTimeInSecond")
In [3]: swimmerDfSorted1.show(6)
```

Here is the output:

```
+----+------+-----------+----------------+
|  id|Gender| Occupation|swimTimeInSecond|
+----+------+-----------+----------------+
| id3|  Male|    Manager|           15.15|
| id4|  Male|RiskAnalyst|           15.27|
| id2|Female|    Manager|           15.56|
| id5|  Male| Programmer|           15.65|
| id6|  Male|RiskAnalyst|           15.74|
|id12|Female|RiskAnalyst|            15.9|
+----+------+-----------+----------------+
only showing top 6 rows
```

We know that the default of `ascending` is `True`, but let’s print this value and we will get the same result.

```
In [4]: swimmerDf.orderBy("swimTimeInSecond", ascending=True).show(6)
```

Here is the output:

```
+----+------+-----------+----------------+
|  id|Gender| Occupation|swimTimeInSecond|
+----+------+-----------+----------------+
| id3|  Male|    Manager|           15.15|
| id4|  Male|RiskAnalyst|           15.27|
| id2|Female|    Manager|           15.56|
| id5|  Male| Programmer|           15.65|
| id6|  Male|RiskAnalyst|           15.74|
|id12|Female|RiskAnalyst|            15.9|
+----+------+-----------+----------------+
only showing top 6 rows
```

#### Step 4-11-2\. Sorting a DataFrame in Descending Order

Here, we will sort the `swimmerDf` DataFrame on the `swimTimeInSecond` column in descending order. For descending order, we have to set the `ascending` key to `False`.

```
In [5]: swimmerDfSorted2 =swimmerDf.orderBy("swimTimeInSecond", ascending=False)
In [6]: swimmerDfSorted2.show(6)
```

Here is the output:

```
+----+------+-----------+----------------+
|  id|Gender| Occupation|swimTimeInSecond|
+----+------+-----------+----------------+
| id8|  Male|    Manager|           17.11|
| id9|Female| Programmer|           16.83|
| id7|Female| Programmer|            16.8|
| id1|  Male| Programmer|           16.73|
|id10|Female|RiskAnalyst|           16.34|
|id11|  Male| Programmer|           15.96|
+----+------+-----------+----------------+
only showing top 6 rows
```

#### Step 4-11-3\. Sorting on Two Columns in Different Order

Here, we sort the `swimmerDf` DataFrame on the `Occupation` and `swimTimeInSecond` columns in descending and ascending order, respectively. In this case, the value of the ascending key is a list, that is `[False,True]`.

```
In [7]: swimmerDfSorted3 = swimmerDf.orderBy("Occupation","swimTimeInSecond",  ascending=[False,True])
In [8]: swimmerDfSorted3.show(6)
```

Here is the output:

```
+----+------+-----------+----------------+
|  id|Gender| Occupation|swimTimeInSecond|
+----+------+-----------+----------------+
| id4|  Male|RiskAnalyst|           15.27|
| id6|  Male|RiskAnalyst|           15.74|
|id12|Female|RiskAnalyst|            15.9|
|id10|Female|RiskAnalyst|           16.34|
| id5|  Male| Programmer|           15.65|
|id11|  Male| Programmer|           15.96|
+----+------+-----------+----------------+
only showing top 6 rows
```

## Recipe 4-12\. Sort Data Partition-Wise

### Problem

You want to sort a DataFrame partition-wise.

### Solution

We know that DataFrames are partitioned over many nodes. Now we want to sort a DataFrame partition-wise. We are going to use the `swimmerDf` DataFrame, which we have used in previous recipes.

In PySpark SQL, partition-wise sorting that’s specified by columns is executed using the `sortWithinPartitions(*cols, **kwargs)` function. The `sortWithinPartitions()` function returns a new DataFrame. Arguments are similar to the `orderBy()` function’s argument.

We have to perform the following:

*   Perform a partition-wise sort on the `swimmerDf` DataFrame. We do this on the `Occupation` and `swimTimeInSecond` columns in descending and ascending order, respectively.

### How It Works

#### Step 4-12-1\. Performing a Partition-Wise Sort (Single Partition Case)

In this step, we sort on the `swimmerDf` DataFrame on the `Occupation` and `swimTimeInSecond` columns in descending and ascending order, respectively. To start, we apply the `sortWithinPartitions()` function :

```
In [1]: sortedPartitons = swimmerDf.sortWithinPartitions("Occupation","swimTimeInSecond", ascending=[False,True])
In [2]: swimmerDf1.show(6)
```

Here is the output:

```
+----+------+-----------+----------------+
|  id|Gender| Occupation|swimTimeInSecond|
+----+------+-----------+----------------+
| id4|  Male|RiskAnalyst|           15.27|
| id6|  Male|RiskAnalyst|           15.74|
|id12|Female|RiskAnalyst|            15.9|
|id10|Female|RiskAnalyst|           16.34|
| id5|  Male| Programmer|           15.65|
|id11|  Male| Programmer|           15.96|
+----+------+-----------+----------------+
only showing top 6 rows
```

It seems that we have achieved the result. But why is this result similar to the result we got in Step 4-11-3 from Recipe 4-11? Let’s check on the number of partitions in this DataFrame. The following line of code shows that it is a single partitioned DataFrame.

```
In [3]: swimmerDf.rdd.getNumPartitions()
```

Here is the output:

```
Out[3]: 1
```

#### Step 4-12-2\. Performing Partition-Wise Sort (Double Partition Case)

Here, we again perform a sort on the `swimmerDf` DataFrame, on the `Occupation` and `swimTimeInSecond` columns , in descending and ascending order, respectively

Now let’s repartition the DataFrame into two partitions. Repartitioning a DataFrame can be done using the `repartition()` function, which looks like `repartition(numPartitions, *cols)`. The first argument is the number of partitions and the second argument is the partitioning expressions. The `repartition()` function shuffles the data. It uses a hash partitioner to shuffle the data across the cluster. Repartitioning and shuffling are shown in Figure [4-8](#469054_1_En_4_Chapter.xhtml#Fig8).

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig8_HTML.jpg](images/469054_1_En_4_Chapter/469054_1_En_4_Fig8_HTML.jpg)

Figure 4-8

Repartitioning and shuffling

Figure [4-8](#469054_1_En_4_Chapter.xhtml#Fig8) displays that the DataFrame on the left is partitioned into two parts—Partition 1 and Partition 2\. A close look at Partition 1 and Partition 2 reveals that the DataFrame is not partitioned simply. Rather, the data has been shuffled. Let’s investigate repartitioning using the PySpark SQL API.

```
In [4]: swimmerDf1 = swimmerDf.repartition(2)
In [5]: swimmerDf1.rdd.glom().collect()
```

Here is the output:

```
Out[6]:
[
[Row(id=u'id3', Gender=u'Male', Occupation=u'Manager', swimTimeInSecond=15.15),
Row(id=u'id10', Gender=u'Female', Occupation=u'RiskAnalyst', swimTimeInSecond=16.34),
Row(id=u'id7', Gender=u'Female', Occupation=u'Programmer', swimTimeInSecond=16.8),
Row(id=u'id5', Gender=u'Male', Occupation=u'Programmer', swimTimeInSecond=15.65),
Row(id=u'id11', Gender=u'Male', Occupation=u'Programmer', swimTimeInSecond=15.96),
Row(id=u'id1', Gender=u'Male', Occupation=u'Programmer', swimTimeInSecond=16.73)],
[Row(id=u'id4', Gender=u'Male', Occupation=u'RiskAnalyst', swimTimeInSecond=15.27),
Row(id=u'id8', Gender=u'Male', Occupation=u'Manager', swimTimeInSecond=17.11),
Row(id=u'id12', Gender=u'Female', Occupation=u'RiskAnalyst', swimTimeInSecond=15.9),
Row(id=u'id6', Gender=u'Male', Occupation=u'RiskAnalyst', swimTimeInSecond=15.74),
Row(id=u'id9', Gender=u'Female', Occupation=u'Programmer', swimTimeInSecond=16.83),
Row(id=u'id2', Gender=u'Female', Occupation=u'Manager', swimTimeInSecond=15.56)]
]
```

The results depict that the DataFrame has been repartitioned into two parts. Now it’s time to perform partition-wise sorting. But before we write the code for partition-wise sorting, let’s look at the process of sorting on repartitioned data.

Figure [4-9](#469054_1_En_4_Chapter.xhtml#Fig9) displays the partition-wise sorting. Just concentrate on the left side and the right side Partition 1 DataFrame partition. In the process of sorting, nothing was shuffled. While sorting, each partition was considered an independent, full DataFrame.

```
In [7]: sortedPartitons = swimmerDf1.sortWithinPartitions("Occupation","swimTimeInSecond", ascending=[False,True])
In [8]: sortedPartitons.show()
```

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig9_HTML.jpg](images/469054_1_En_4_Chapter/469054_1_En_4_Fig9_HTML.jpg)

Figure 4-9

Partitioning and sorting

Here is the output:

```
+----+------+-----------+----------------+
|  id|Gender| Occupation|swimTimeInSecond|
+----+------+-----------+----------------+
|id10|Female|RiskAnalyst|           16.34|
| id5|  Male| Programmer|           15.65|
|id11|  Male| Programmer|           15.96|
| id1|  Male| Programmer|           16.73|
| id7|Female| Programmer|            16.8|
| id3|  Male|    Manager|           15.15|
| id4|  Male|RiskAnalyst|           15.27|
| id6|  Male|RiskAnalyst|           15.74|
|id12|Female|RiskAnalyst|            15.9|
| id9|Female| Programmer|           16.83|
| id2|Female|    Manager|           15.56|
| id8|  Male|    Manager|           17.11|
+----+------+-----------+----------------+
In [8]: sortedPartitons.rdd.glom().collect()
```

Here is the output:

```
Out[19]:
[[Row(id=u'id10', Gender=u'Female', Occupation=u'RiskAnalyst', swimTimeInSecond=16.34),
Row(id=u'id5', Gender=u'Male', Occupation=u'Programmer', swimTimeInSecond=15.65),
Row(id=u'id11', Gender=u'Male', Occupation=u'Programmer', swimTimeInSecond=15.96),
Row(id=u'id1', Gender=u'Male', Occupation=u'Programmer', swimTimeInSecond=16.73),
Row(id=u'id7', Gender=u'Female', Occupation=u'Programmer', swimTimeInSecond=16.8),
Row(id=u'id3', Gender=u'Male', Occupation=u'Manager', swimTimeInSecond=15.15)],
[Row(id=u'id4', Gender=u'Male', Occupation=u'RiskAnalyst', swimTimeInSecond=15.27),
Row(id=u'id6', Gender=u'Male', Occupation=u'RiskAnalyst', swimTimeInSecond=15.74),
Row(id=u'id12', Gender=u'Female', Occupation=u'RiskAnalyst', swimTimeInSecond=15.9),
Row(id=u'id9', Gender=u'Female', Occupation=u'Programmer', swimTimeInSecond=16.83),
Row(id=u'id2', Gender=u'Female', Occupation=u'Manager', swimTimeInSecond=15.56),
Row(id=u'id8', Gender=u'Male', Occupation=u'Manager', swimTimeInSecond=17.11)]]
```

This output should make the concept very clear. The records with `id` column values `id10`, `id5`, `id11`, `id1`, `id7`, and `id3` are in the first partition and the rest are in the second partition.

## Recipe 4-13\. Remove Duplicate Records from a DataFrame

### Problem

You want to remove duplicate records from a DataFrame.

### Solution

In order to remove duplicates, we are going to use the `drop_duplicates()` function. This function can remove duplicated data conditioned on some column. If no column is specified as input, all the records in all the columns are checked.

Figure [4-10](#469054_1_En_4_Chapter.xhtml#Fig10) shows that some records are duplicates. If we conditioned duplicate removal on columns `iv1` and `iv2` we can see that many records are duplicates. We have to perform the following:

*   Remove all the duplicate records.

*   Remove all the duplicate records conditioned on column `iv1`.

*   Remove all the duplicate records conditioned on columns `iv1` and `iv2`.

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig10_HTML.jpg](images/469054_1_En_4_Chapter/469054_1_En_4_Fig10_HTML.jpg)

Figure 4-10

Sample data

### How It Works

#### Step 4-13-1\. Removing Duplicate Records

We are going to read our data from the ORC file `duplicateData`.

```
In [1]: duplicateDataDf = spark.read.orc(path='duplicateData')
In [2]: duplicateDataDf.show(6)
```

Here is the output:

```
+---+---+-----+
|iv1|iv2|  iv3|
+---+---+-----+
| c1| d2|  9.8|
| c1| d2| 8.36|
| c1| d2| 9.06|
| c1| d2|11.15|
| c1| d2| 6.26|
| c2| d2| 8.74|
+---+---+-----+
only showing top 6 rows
```

We have created the DataFrame successfully. Now, we are going to drop all the duplicate records. We can see that there are 20 records in this DataFrame. We can verify the number of records using the `count` function, as follows.

```
In [3]: duplicateDataDf.count()
```

Here is the output:

```
Out[3]: 20
```

Let’s drop all the duplicate records.

```
In [4]: noDuplicateDf1 = duplicateDataDf.drop_duplicates()
In [5]: noDuplicateDf1.show()
```

Here is the output:

```
+---+---+-----+
|iv1|iv2|  iv3|
+---+---+-----+
| c1| d2|  9.8|
| c1| d2|11.15|
| c2| d1| 8.16|
| c2| d1|12.88|
| c2| d2|10.79|
| c2| d2| 8.74|
| c1| d2|13.34|
| c1| d1|  9.8|
| c2| d1|11.15|
| c1| d2| 9.06|
| c1| d2| 7.99|
| c2| d1|10.44|
| c2| d1| 11.0|
| c1| d1| 9.97|
| c2| d1| 9.92|
| c1| d2| 8.74|
| c1| d2| 6.26|
| c1| d2| 8.36|
+---+---+-----+
In [6]: noDuplicateDf1.count()
```

Here is the output:

```
Out[6]: 18
```

It is clear that the total number of records after duplicate removal is 18\. The duplicate data has been removed.

#### Step 4-13-2\. Removing the Duplicate Records Conditioned on column iv1

From the `duplicateDataDf` DataFrame it is clear that column `iv1` has two values—`c1` and `c2`. Therefore, the final DataFrame, after duplicate removal, will have only two records. The first record will be `c1` and the second record will be `c2`.

```
In [7]: noDuplicateDf2 = duplicateDataDf.drop_duplicates(['iv1'])
In [8]: noDuplicateDf2.show()
```

Here is the output:

```
+---+---+----+
|iv1|iv2| iv3|
+---+---+----+
| c1| d2| 9.8|
| c2| d2|8.74|
+---+---+----+
```

The `noDuplicateDf2` DataFrame shows only two records. The first record has `c1` in its `iv1` column and the second record has `c2` in the `iv1` column.

#### Step 4-13-3\. Removing the Duplicate Records Conditioned on Columns iv1 and iv2

The `iv1` column has two distinct values—`c1` and `c2`. Similarly, column `iv2` has two distinct values—`d1` and `d2`. That makes four distinct combinations and those are (c1, d1), (c1, d2), (c3, d3), and (c4, d4). Therefore, if we drop the duplicates conditioned on column `iv1` and `iv2`, the final result will show four records.

```
In [9]: noDuplicateDf3 = duplicateDataDf.drop_duplicates(['iv1','iv2'])
In [10]: noDuplicateDf3.show()
```

Here is the output:

```
+---+---+----+
|iv1|iv2| iv3|
+---+---+----+
| c2| d1|9.92|
| c1| d2| 9.8|
| c2| d2|8.74|
| c1| d1|9.97|
+---+---+----+
```

In the final output, we have four records, as expected from this discussion.

## Recipe 4-14\. Sample Records

### Problem

You want to sample some records from a given DataFrame.

### Solution

Working on huge amounts of data is time-intensive and computation-intensive, even for a framework like PySpark. Sometimes data scientists instead get samples of data from an actual dataset and apply data science operations on that.

PySpark SQL provides tools that can gather sample from a given dataset. There are two DataFrame functions that can be applied to get samples from DataFrames. The first function— `sample(withReplacement, fraction, seed=None)` —returns a sample from a DataFrame. This function returns a new DataFrame. Its first argument, `withReplacement`, specifies that, if you need duplicate records in sampled data, the second argument fraction is the sampling fraction. Since that sample is taken randomly using some random number mechanism, the seed is used in the random number generation internally.

The second function— `sampleBy(col, fractions, seed=None)` —will perform stratified sampling conditioned on some column of the DataFrame which we provide as the `col` argument. The `fractions` argument is used to provide a sample fraction of each strata. This argument takes its value as a dictionary. The `seed` argument has the same meaning as before. See Figure [4-11](#469054_1_En_4_Chapter.xhtml#Fig11).

![../images/469054_1_En_4_Chapter/469054_1_En_4_Fig11_HTML.jpg](images/469054_1_En_4_Chapter/469054_1_En_4_Fig11_HTML.jpg)

Figure 4-11

Sample data

We are going to use the `noDuplicateDf1` DataFrame that we created in Recipe 4-13\. We have to perform the following:

*   Sample data from the `noDuplicateDf1` DataFrame without replacement.

*   Sample data from the `noDuplicateDf1` DataFrame with replacement.

*   Sample data from the `noDuplicateDf1` DataFrame conditioned on the first column, called `iv1`.

### How It Works

#### Step 4-14-1\. Sampling Data from the noDuplicateDf1 DataFrame Without Replacement

Let’s print some records from the `noDuplicateDf1` DataFrame, since this will refresh our memory about the DataFrame structure.

```
In [1]: noDuplicateDf1.show(6)
```

Here is the output:

```
+---+---+-----+
|iv1|iv2|  iv3|
+---+---+-----+
| c1| d2|  9.8|
| c1| d2|11.15|
| c2| d1| 8.16|
| c2| d1|12.88|
| c2| d2|10.79|
| c2| d2| 8.74|
+---+---+-----+
only showing top 6 rows
```

We know from the previous recipe that it has 18 records.

```
In [2]: noDuplicateDf1.count()
```

Here is the output:

```
Out[2]: 18
```

We are going to fetch 50% of the records as a sample without replacement.

```
In [3]: sampleWithoutKeyConsideration = noDuplicateDf1.sample(withReplacement=False, fraction=0.5, seed=200)
In [4]: sampleWithoutKeyConsideration.show()
```

Here is the output:

```
+---+---+-----+
|iv1|iv2|  iv3|
+---+---+-----+
| c1| d2|  9.8|
| c1| d2|11.15|
| c2| d1|12.88|
| c2| d2|10.79|
| c2| d2| 8.74|
| c2| d1| 11.0|
| c1| d1| 9.97|
| c1| d2| 6.26|
+---+---+-----+
```

Now we do the count:

```
In [5]: sampleWithoutKeyConsideration.count()
```

Here is the output:

```
Out[5]: 8
```

We fetched eight records in our sample DataFrame `sampleWithoutKeyConsideration`. The total number of records in the parent DataFrame `noDuplicateDf1` is 18. We have asked for 50%, which means nine records, but we have only eight records. Remember that the output of the `sample()` function does not follow the exact fraction value.

#### Step 4-14-2\. Sampling Data from the noDuplicateDf1 DataFrame with Replacements

In the following line of code, we provide the value of the `withReplacement` argument in the `sample` function as `True`. Due to this, we are going to get duplicate records in the output.

```
In [6]: sampleWithoutKeyConsideration1 = noDuplicateDf1.sample(withReplacement=True, fraction=0.5, seed=200)
In [7]: sampleWithoutKeyConsideration1.count()
```

Here is the output:

```
Out[7]: 6
In [8]: sampleWithoutKeyConsideration1.show()
```

Here is the output:

```
+---+---+-----+
|iv1|iv2|  iv3|
+---+---+-----+
| c1| d2|13.34|
| c1| d2|13.34|
| c1| d2|13.34|
| c2| d1|10.44|
| c2| d1| 11.0|
| c1| d2| 8.74|
+---+---+-----+
```

As expected, the first three records are the same (duplicates of each other) in the `sampleWithoutKeyConsideration1` DataFrame output.

#### Step 4-14-3\. Sampling Data from the noDuplicateDf1 DataFrame Conditioned on the iv1 Column

Column `iv1` has two values—`c1` and `c2`. To perform stratified sampling, we have to use the `sampleBy()` function. Now we are going to condition on column `iv1`, which we are providing as a value of the argument `col` to the `sampleBy()` function. We are looking for equal representatives in output DataFrame from strata `c1` and `c2`. Therefore, we have provided `{'c1':0.5, 'c2':0.5}` as the value of fractions.

```
In [9]: sampleWithKeyConsideration = noDuplicateDf1.sampleBy(col='iv1', fractions={'c1':0.5, 'c2':0.5},seed=200)
In [10]: sampleWithKeyConsideration.show()
+---+---+-----+
|iv1|iv2|  iv3|
+---+---+-----+
| c1| d2|  9.8|
| c1| d2|11.15|
| c2| d1|12.88|
| c2| d2|10.79|
| c2| d2| 8.74|
| c2| d1| 11.0|
| c1| d1| 9.97|
| c1| d2| 6.26|
+---+---+-----+
```

We can observe an equal number of representatives from strata `c1` and `c2`.

## Recipe 4-15\. Find Frequent Items

### Problem

You want to determine which items appear most frequently in the columns of the DataFrame.

### Solution

In order to see the frequent items, we are going to use the `freqItems()` function.

### How It Works

First we are going to calculate the frequent items in column `iv1`.

```
In [1]: duplicateDataDf.freqItems(cols=['iv1']).show()
```

Here is the output:

```
+-------------+
|iv1_freqItems|
+-------------+
|     [c1, c2]|
+-------------+
```

Now we are going to determine the frequent items in the `iv1` and `iv2` columns.

```
In [2]: duplicateDataDf.freqItems(cols=['iv1','iv2']).show()
+-------------+-------------+
|iv1_freqItems|iv2_freqItems|
+-------------+-------------+
|     [c1, c2]|     [d2, d1]|
+-------------+-------------+
```

# 5. Data Merging and Data Aggregation Using PySparkSQL

Data merging and data aggregation are an essential part of the day-to-day activities of PySparkSQL users. This chapter will discuss and describe the following recipes.

*   Recipe 5-1\. Aggregate data on a single key

*   Recipe 5-2\. Aggregate data on multiple keys

*   Recipe 5-3\. Create a contingency table

*   Recipe 5-4\. Perform joining operations on two DataFrames

*   Recipe 5-5\. Vertically stack two DataFrames

*   Recipe 5-6\. Horizontally stack two DataFrames

*   Recipe 5-7\. Perform missing value imputation

## Recipe 5-1\. Aggregate Data on a Single Key

### Problem

You want to perform data aggregation on a DataFrame, grouped on a single key.

### Solution

We perform data aggregation to observe summary of data.

The data has been taken from an R `datasets` package. The name of the data is `UCBAdmissions`. This dataset is in the form of R tables. I transformed it into tabular form and saved it in a table of a MySQL database. We have to read this table using PySparkSQL and perform the following:

*   Mean value of the number of accepted and rejected students

*   Mean value of students gender-wise who have applied for admission

*   The average frequency of application gender-wise

In order to run the aggregation API, we have to first group the data if the aggregation has to be done on some column. In order to group a column based on a data condition, we have to use the `groupby()` function . After grouping the data, we can apply the following aggregation functions to it.

*   `mean()`

*   `count()`

*   `sum()`

*   `min()`

*   `max()`

*   etc ….

### How It Works

#### Step 5-1-1\. Reading the Data from MySQL

The admission data is in the `ucbdata` table of the `pysparksqlbook` database of MySQL. We can read it using the following line of code.

```
In [1]: dbURL = "jdbc:mysql://localhost/pysparksqlbook"
In [2]: ucbDataFrame = spark.read.format("jdbc").options(url = dbURL, database ='pysparksqlbook', dbtable ='ucbdata', user="root", password="").load();
In [3]: ucbDataFrame.show()
```

Here is the output:

```
+--------+------+----------+---------+
|   admit|gender|department|frequency|
+--------+------+----------+---------+
|Admitted|  Male|         A|      512|
|Rejected|  Male|         A|      313|
|Admitted|Female|         A|       89|
|Rejected|Female|         A|       19|
|Admitted|  Male|         B|      353|
|Rejected|  Male|         B|      207|
|Admitted|Female|         B|       17|
|Rejected|Female|         B|        8|
|Admitted|  Male|         C|      120|
|Rejected|  Male|         C|      205|
|Admitted|Female|         C|      202|
|Rejected|Female|         C|      391|
|Admitted|  Male|         D|      138|
|Rejected|  Male|         D|      279|
|Admitted|Female|         D|      131|
|Rejected|Female|         D|      244|
|Admitted|  Male|         E|       53|
|Rejected|  Male|         E|      138|
|Admitted|Female|         E|       94|
|Rejected|Female|         E|      299|
+--------+------+----------+---------+
only showing top 20 rows
```

#### Step 5-1-2\. Calculating the Required Means

We need the mean value of the number of accepted and admitted students. We are going to group data on the `admit` column using the `groupby()` function. Thereafter, we are going to apply the `mean()` function.

```
In [4]: groupedOnAdmit = ucbDataFrame.groupby(["admit"]).mean()
In [5]: groupedOnAdmit.show()
```

Here is the output:

```
+--------+------------------+
|   admit|    avg(frequency)|
+--------+------------------+
|Rejected|230.91666666666666|
|Admitted|            146.25|
+--------+------------------+
```

#### Step 5-1-3\. Grouping by Gender

Now we want the mean value of students gender-wise who have applied for admission. We are going to group data on the `gender` column using the `groupby()` function. Thereafter, we are going to apply the `mean()` function.

```
In [6]: groupedOnGender = ucbDataFrame.groupby(["gender"]).mean()
In [7]: groupedOnGender.show()
```

Here is the output:

```
+------+------------------+
|gender|    avg(frequency)|
+------+------------------+
|Female|152.91666666666666|
|  Male|            224.25|
+------+------------------+
```

#### Step 5-1-4\. Finding the Average Frequency of Application by Gender

We are going to group the data on the `department` column using the `groupby()` function. Thereafter, we are going to apply the `mean()` function.

```
In [8]: groupedOnDepartment = ucbDataFrame.groupby(["department"]).mean()
In [9]: groupedOnDepartment.show()
+----------+--------------+
|department|avg(frequency)|
+----------+--------------+
|         F|         178.5|
|         E|         146.0|
|         B|        146.25|
|         D|         198.0|
|         C|         229.5|
|         A|        233.25|
+----------+--------------+
```

## Recipe 5-2\. Aggregate Data on Multiple Keys

### Problem

You want to perform data aggregation on a DataFrame, grouped on multiple keys.

### Solution

We are going to use the admission data from the previous recipe. In order to aggregate data conditioned on multiple columns, we have to group it on multiple columns. We can use the `groupby()` function to group data conditioned on multiple columns. We do this with the following steps:

*   Group the data on the `admit` and `gender` columns and find the mean of applications to the college.

*   Group the data on the `admit` and `department` columns and find the mean of applications to the college.

### How It Works

Let’s print the UCB admission data.

```
In [1]: ucbDataFrame.show()
```

Here is the output:

```
+--------+------+----------+---------+
|   admit|gender|department|frequency|
+--------+------+----------+---------+
|Admitted|  Male|         A|      512|
|Rejected|  Male|         A|      313|
|Admitted|Female|         A|       89|
|Rejected|Female|         A|       19|
|Admitted|  Male|         B|      353|
|Rejected|  Male|         B|      207|
|Admitted|Female|         B|       17|
|Rejected|Female|         B|        8|
|Admitted|  Male|         C|      120|
|Rejected|  Male|         C|      205|
|Admitted|Female|         C|      202|
|Rejected|Female|         C|      391|
|Admitted|  Male|         D|      138|
|Rejected|  Male|         D|      279|
|Admitted|Female|         D|      131|
|Rejected|Female|         D|      244|
|Admitted|  Male|         E|       53|
|Rejected|  Male|         E|      138|
|Admitted|Female|         E|       94|
|Rejected|Female|         E|      299|
+--------+------+----------+---------+
only showing top 20 rows
```

#### Step 5-2-1\. Grouping the Data on Gender and Finding the Mean of Applications

We are going to group the data on the `admit` and `gender` columns using the `groupby()` function. Thereafter, we are going to apply the `mean()` function.

```
In [2]: groupedOnAdmitGender =ucbDataFrame.groupby( [ "admit" , "gender  "]  ).mean( )
In [3]: groupedOnAdmitGender.show()
```

Here is the output:

```
+--------+------+------------------+
|   admit|gender|    avg(frequency)|
+--------+------+------------------+
|Rejected|Female|             213.0|
|Rejected|  Male|248.83333333333334|
|Admitted|Female| 92.83333333333333|
|Admitted|  Male|199.66666666666666|
+--------+------+------------------+
```

#### Step 5-2-2\. Grouping the Data on Department and Finding the Mean of Applications

We are going to group the data on the `admit` and `department` columns using the `groupby()` function. Thereafter, we are going to apply the `mean()` function.

```
In [4]: groupedOnAdmitDepartment = ucbDataFrame.groupby([ "admit", "department" ]).mean()
In [5]: groupedOnAdmitDepartment.show()
```

Here is the output:

```
+--------+----------+--------------+
|   admit|department|avg(frequency)|
+--------+----------+--------------+
|Admitted|         C|         161.0|
|Admitted|         E|          73.5|
|Rejected|         A|         166.0|
|Admitted|         B|         185.0|
|Admitted|         F|          23.0|
|Admitted|         A|         300.5|
|Rejected|         C|         298.0|
|Rejected|         D|         261.5|
|Admitted|         D|         134.5|
|Rejected|         F|         334.0|
|Rejected|         B|         107.5|
|Rejected|         E|         218.5|
+--------+----------+--------------+
```

## Recipe 5-3\. Create a Contingency Table

### Problem

You want to create a contingency table.

### Solution

Contingency tables are also known as cross tabulations. They show the pairwise frequency of the given columns.

The owner of a restaurant wants to know about the service provided. She surveys some customers and gets the result shown in Figure [5-1](#469054_1_En_5_Chapter.xhtml#Fig1). This data is in a MongoDB collection called `restaurantSurvey` . We can observe it using the following MongoDB command.

![../images/469054_1_En_5_Chapter/469054_1_En_5_Fig1_HTML.jpg](images/469054_1_En_5_Chapter/469054_1_En_5_Fig1_HTML.jpg)

Figure 5-1

Contingency table for the restaurant survey

```
> db.restaurantSurvey.find().pretty().limit(5)
```

Here is the output:

```
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78a"),
"Gender" : "Male",
"Vote" : "Yes"
}
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78b"),
"Gender" : "Male",
"Vote" : "Yes"
}
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78c"),
"Gender" : "Male",
"Vote" : "No"
}
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78d"),
"Gender" : "Male",
"Vote" : "DoNotKnow"
}
{
"_id" : ObjectId("5ba7e6a259acc01fedb4d78e"),
"Gender" : "Male",
"Vote" : "Yes"
}
```

We have to make a contingency table using the data in Figure [5-1](#469054_1_En_5_Chapter.xhtml#Fig1).

### How It Works

#### Step 5-3-1\. Reading Restaurant Survey Data from MongoDB

The restaurant survey data is in a `restaurantSurvey` collection of the `pysparksqlbook` database in MongoDB. The following line of code will read our data from MongoDB.

```
In [1]: surveyDf = spark.read.format("com.mongodb.spark.sql.DefaultSource").option("uri","mongodb://127.0.0.1/pysparksqlbook.restaurantSurvey").load()
In [2]: surveyDf.show(5)
```

Here is the output:

```
+------+---------+--------------------+
|Gender|     Vote|                 _id|
+------+---------+--------------------+
|  Male|      Yes|[5ba7e6a259acc01f...|
|  Male|      Yes|[5ba7e6a259acc01f...|
|  Male|       No|[5ba7e6a259acc01f...|
|  Male|DoNotKnow|[5ba7e6a259acc01f...|
|  Male|      Yes|[5ba7e6a259acc01f...|
+------+---------+--------------------+
only showing top 5 rows
```

We have read our data. Since the `_id` column is not required, we are going to drop it.

```
In [3]: surveyDf = surveyDf.drop("_id")
In [4]: surveyDf.show(5)
```

Here is the output:

```
+------+---------+
|Gender|     Vote|
+------+---------+
|  Male|      Yes|
|  Male|      Yes|
|  Male|       No|
|  Male|DoNotKnow|
|  Male|      Yes|
+------+---------+
only showing top 5 rows
```

#### Step 5-3-2\. Creating a Contingency Table

In order to create a contingency table, we have to use the `crosstab()` function. This function takes two columns as arguments.

```
In [5]: surveyDf.crosstab("Gender","Vote").show()
```

Here is the output:

```
+-----------+---------+--+---+
|Gender_Vote|DoNotKnow|No|Yes|
+-----------+---------+--+---+
|       Male|        2| 5|  5|
|     Female|        1| 1|  6|
+-----------+---------+--+---+
```

We have our contingency table.

## Recipe 5-4\. Perform Joining Operations on Two DataFrames

### Problem

You want to perform a join operation on two DataFrames.

### Solution

Data scientists often need to merge the content of two DataFrames using joining operations. For people who use SQL, DataFrame joining is the same as table joining. It is as simple as any other operation on DataFrames. We perform the following four types of joins on DataFrames.

*   Inner join

*   Left outer join

*   Right outer join

*   Full outer join

We have been given two tables in a Cassandra database. The data of first table is displayed in Figure [5-2](#469054_1_En_5_Chapter.xhtml#Fig2). The first table is called `students`.

![../images/469054_1_En_5_Chapter/469054_1_En_5_Fig2_HTML.jpg](images/469054_1_En_5_Chapter/469054_1_En_5_Fig2_HTML.jpg)

Figure 5-2

The students table

The second table is shown in Figure [5-3](#469054_1_En_5_Chapter.xhtml#Fig3). Its name is `subjects`.

![../images/469054_1_En_5_Chapter/469054_1_En_5_Fig3_HTML.jpg](images/469054_1_En_5_Chapter/469054_1_En_5_Fig3_HTML.jpg)

Figure 5-3

The subjects table

We have to perform the following.

*   Read the `students` and `subjects` tables from the Cassandra database.

*   Perform an inner join on the DataFrames.

*   Perform an left outer join on the DataFrames.

*   Perform a right outer join on the DataFrames.

*   Perform a full outer join on the DataFrames.

In order to join the two DataFrames, PySparkSQL provides a `join()` function, which works on the DataFrames.

The `join()` function takes three arguments. The first argument, called `other`, is the other DataFrame. The second argument, called `on`, defines the key columns on which we want to perform joining operations. The third argument, called `how`, defines which join type to perform.

### How It Works

Let’s explore this recipe in a step-by-step fashion.

#### Step 5-4-1\. Reading Student and Subject Data Tables from a Cassandra Database

We need to read the `students` data in the `pysparksqlbook` keyspace of the `students` table. Similarly, we need to read the `subjects` data in the same keyspace, but in a Cassandra table called `subjects`.

The following lines will read the data tables from the Cassandra database.

```
In [1]: studentsDf = spark.read.format("org.apache.spark.sql.cassandra").options( keyspace="pysparksqlbook", table="students").load()
In [2]: subjectsDf = spark.read.format( "org.apache.spark.sql.cassandra").options( keyspace="pysparksqlbook", table="subjects").load()
```

After reading data from Cassandra, let’s print it and verify that we have created a DataFrame.

```
In [3]: studentsDf.show()
```

Here is the output:

```
+---------+------+-------+
|studentid|gender|   name|
+---------+------+-------+
|      si3|     F|  Julie|
|      si2|     F|  Maria|
|      si1|     M|  Robin|
|      si6|     M|William|
|      si4|     M|    Bob|
+---------+------+-------+
In [4]: subjectsDf.show()
```

Here is the output:

```
+--+-----+---------+--------+
|id|marks|studentid|subjects|
+--+-----+---------+--------+
| 6|   78|      si4|     C++|
| 9|   83|      si2|    Java|
| 5|   72|      si3|    Ruby|
| 4|   85|      si2|  Python|
| 7|   77|      si5|       C|
| 1|   75|      si1|  Python|
| 8|   84|      si4|  Python|
| 2|   76|      si3|    Java|
| 3|   81|      si1|    Java|
+--+-----+---------+--------+
```

We have created our DataFrames. The `subjectsDf` DataFrame contains a column called `id`, which is not required. We can drop it. To drop unwanted columns, use the `drop()` function .

```
In [5]: subjectsDf = subjectsDf.drop("id")
In [6]: subjectsDf.show()
```

Here is the output:

```
+-----+---------+--------+
|marks|studentid|subjects|
+-----+---------+--------+
|   78|      si4|     C++|
|   83|      si2|    Java|
|   72|      si3|    Ruby|
|   85|      si2|  Python|
|   77|      si5|       C|
|   75|      si1|  Python|
|   84|      si4|  Python|
|   76|      si3|    Java|
|   81|      si1|    Java|
+-----+---------+--------+
```

We have dropped the unwanted column.

#### Step 5-4-2\. Performing an Inner Join on DataFrames

At this moment, we have two DataFrames called `studentsDf` and `subjectsDf`. We have to perform inner joins on these DataFrames. We have a `studentid` column that’s common in both DataFrames. We are going to perform an inner join on that `studentid` column.

Inner joins return records when key values in those records match. If we look at values in the `studentid` column in both DataFrames, we will find that the values `si1`, `si2`, `si3`, and `si4` are common to both DataFrames. Therefore, an inner join will return records only for those values.

```
In [7]: innerDf = studentsDf.join(subjectsDf, studentsDf.studentid == subjectsDf.studentid, how= "inner")
```

Let’s explore the argument of the `join()` function. The first argument is `subjectsDf`, which is the DataFrame that’s getting joined with the `studentsDf` DataFrame. The second argument is `studentsDf.studentid == subjectsDf.studentid`, which indicates the condition of joining. The last argument tells us that we have to perform an inner join.

Let’s print the result.

```
In [8]: innerDf.show()
```

Here is the output:

```
+---------+------+-----+-----+---------+--------+
|studentid|gender| name|marks|studentid|subjects|
+---------+------+-----+-----+---------+--------+
|      si2|     F|Maria|   83|      si2|    Java|
|      si2|     F|Maria|   85|      si2|  Python|
|      si4|     M|  Bob|   78|      si4|     C++|
|      si4|     M|  Bob|   84|      si4|  Python|
|      si3|     F|Julie|   72|      si3|    Ruby|
|      si3|     F|Julie|   76|      si3|    Java|
|      si1|     M|Robin|   75|      si1|  Python|
|      si1|     M|Robin|   81|      si1|    Java|
+---------+------+-----+-----+---------+--------+
```

In the output DataFrame `innerDf`, we can observe that in the `studentid` column, we see only `si1`, `si2`, `si3`, and `si4` .

#### Step 5-4-3\. Performing a Left Outer Join on DataFrames

In this step of recipe, we are going to perform the `left` outer join on the DataFrames. In a `left` outer join, each key for the first DataFrame and matched keys from the second DataFrame will be in the result.

In our case, we are using `studentsDf` as the first DataFrame and `subjectsDf` as the second DataFrame when joining. So every value in the `studentid` column of `studentsDf` will be shown in the output. In the `studentid` column, we show `si1`, `si2`, `si3`, `si4`, and `si6`.

```
In [9]: leftOuterDf = studentsDf.join(subjectsDf, studentsDf.studentid == subjectsDf.studentid, how= "left")
```

It can be observed that the value of the third argument is left for the left outer join.

```
In [10]: leftOuterDf.show()
```

Here is the output:

```
+---------+------+-------+-----+---------+--------+
|studentid|gender|   name|marks|studentid|subjects|
+---------+------+-------+-----+---------+--------+
|      si2|     F|  Maria|   83|      si2|    Java|
|      si2|     F|  Maria|   85|      si2|  Python|
|      si4|     M|    Bob|   78|      si4|     C++|
|      si4|     M|    Bob|   84|      si4|  Python|
|      si3|     F|  Julie|   72|      si3|    Ruby|
|      si3|     F|  Julie|   76|      si3|    Java|
|      si6|     M|William| null|     null|    null|
|      si1|     M|  Robin|   75|      si1|  Python|
|      si1|     M|  Robin|   81|      si1|    Java|
+---------+------+-------+-----+---------+--------+
```

The `studentid` column in the resulting DataFrame shows `si1`, `si2`, `si3`, `si4`, and `si6` .

#### Step 5-4-4\. Performing a Right Outer Join on DataFrames

The following line of code will perform a right outer join. In order to perform a right join, the value the of the `how` argument will be `right`.

```
In [11]: rightOuterDf = studentsDf.join(subjectsDf, studentsDf.studentid == subjectsDf.studentid, how= "right")
In [12]: rightOuterDf.show()
```

Here is the output:

```
+---------+------+-----+-----+---------+--------+
|studentid|gender| name|marks|studentid|subjects|
+---------+------+-----+-----+---------+--------+
|     null|  null| null|   77|      si5|       C|
|      si2|     F|Maria|   83|      si2|    Java|
|      si2|     F|Maria|   85|      si2|  Python|
|      si4|     M|  Bob|   78|      si4|     C++|
|      si4|     M|  Bob|   84|      si4|  Python|
|      si3|     F|Julie|   72|      si3|    Ruby|
|      si3|     F|Julie|   76|      si3|    Java|
|      si1|     M|Robin|   75|      si1|  Python|
|      si1|     M|Robin|   81|      si1|    Java|
+---------+------+-----+-----+---------+--------+
```

The result of the right outer join is `rightOuterDf` .

#### Step 5-4-5\. Performing a Full Outer Join on DataFrames

In a full outer join, all values from the `studentid` column of the two DataFrames will be in the result.

```
In [13]: outerDf = studentsDf.join(subjectsDf, studentsDf.studentid == subjectsDf.studentid, how= "outer")
In [14]: outerDf.show()
```

Here is the output:

```
+---------+------+-------+-----+---------+--------+
|studentid|gender|   name|marks|studentid|subjects|
+---------+------+-------+-----+---------+--------+
|     null|  null|   null|   77|      si5|       C|
|      si2|     F|  Maria|   83|      si2|    Java|
|      si2|     F|  Maria|   85|      si2|  Python|
|      si4|     M|    Bob|   78|      si4|     C++|
|      si4|     M|    Bob|   84|      si4|  Python|
|      si3|     F|  Julie|   72|      si3|    Ruby|
|      si3|     F|  Julie|   76|      si3|    Java|
|      si6|     M|William| null|     null|    null|
|      si1|     M|  Robin|   75|      si1|  Python|
|      si1|     M|  Robin|   81|      si1|    Java|
+---------+------+-------+-----+---------+--------+
```

## Recipe 5-5\. Vertically Stack Two DataFrames

### Problem

You want to perform vertical stacking on two DataFrames.

### Solution

Two or more DataFrames can be stacked, one above the other. This process is known as vertical stacking of DataFrames, and is shown in Figure [5-4](#469054_1_En_5_Chapter.xhtml#Fig4).

![../images/469054_1_En_5_Chapter/469054_1_En_5_Fig4_HTML.jpg](images/469054_1_En_5_Chapter/469054_1_En_5_Fig4_HTML.jpg)

Figure 5-4

Vertically stacking DataFrames

Figure [5-4](#469054_1_En_5_Chapter.xhtml#Fig4) depicts the vertical stacking of two DataFrames. On the left, we can see two DataFrames called `dfOne` and `dfTwo`. DataFrame `dfOne` consists of three columns and four rows. Similarly, DataFrame `dfTwo` also has three row and three columns. The right side of Figure [5-4](#469054_1_En_5_Chapter.xhtml#Fig4) displays the DataFrame that’s been created by vertically stacking the `dfOne` and `dfTwo` DataFrames.

The `firstverticaltable` and `secondverticaltable` tables are in a PostgreSQL database. We can get the tables using the following SQL commands.

```
pysparksqldb=#  select * from firstverticaltable;
```

Here is the output:

```
iv1|  iv2|  iv3
-----+-----+-----
9|11.43|10.25
10.26| 8.35| 9.94
9.84| 9.28| 9.22
11.77|10.18|11.02
(4 rows)
pysparksqldb=# select * from secondverticaltable ;
```

Here is the output:

```
iv1|  iv2|  iv3
-----+-----+-----
11|12.64|12.18
12.26|10.84|12.19
11.84|13.43| 11.6
(3 rows)
```

We have to perform the following:

*   We have to read two tables—`firstverticaltable` and `secondverticaltable`—from the PostgreSQL database and create DataFrames.

*   We have to vertically stack the newly created DataFrames.

We can perform vertical stacking using the `union()` function . The `union()` function takes only one input, and that is another DataFrame. This function is equivalent to `UNION ALL` in SQL. The resultant DataFrame of the `union()` function might have duplicate records. It is therefore suggested that we use the `distinct()` function on the result of a `union()` function .

### How It Works

The first step is to read the data tables from PostgreSQL and create the DataFrames.

#### Step 5-5-1\. Reading Tables from PostgreSQL

### Note

In order to connect with the PostgreSQL database, we have to start a PySpark shell with the following options:

`$pyspark --driver-class-path ~/.ivy2/jars/org.postgresql_postgresql-42.2.4.jar --packages org.postgresql:postgresql:42.2.4`

Now it’s time to read our tables. Our tables are in `pysparksqldb` of a PostgreSQL database. The following line of code will read from PostgreSQL.

```
In [1]: dbURL = "jdbc:postgresql://localhost/pysparksqldb?user=postgres&password=""
In [1]: verticalDfOne = spark.read.format("jdbc").options(url = dbURL, database ='pysparksqldb', dbtable ='firstverticaltable').load();
In [2]: verticalDfOne.show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.26| 8.35| 9.94|
| 9.84| 9.28| 9.22|
|11.77|10.18|11.02|
+-----+-----+-----+
```

We have read the `firstverticaltable` table. Now let’s read another table.

```
In [3]: verticalDfTwo = spark.read.format("jdbc").options(url = dbURL, database ='pysparksqldb', dbtable ='secondverticaltable').load();
In [4]: verticalDfTwo.show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
| 11.0|12.64|12.18|
|12.26|10.84|12.19|
|11.84|13.43| 11.6|
+-----+-----+-----+
```

#### Step 5-5-2\. Performing Vertical Stacking of Newly Created DataFrames

We have created two DataFrames, called `verticalDfOne` and `verticalDfTwo`. We are going to perform vertical stacking of these DataFrames using the `union()` function.

```
In [5]: vstackedDf = verticalDfOne.union(verticalDfTwo)
In [6]: vstackedDf.show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.26| 8.35| 9.94|
| 9.84| 9.28| 9.22|
|11.77|10.18|11.02|
| 11.0|12.64|12.18|
|12.26|10.84|12.19|
|11.84|13.43| 11.6|
+-----+-----+-----+
```

Finally, we have a vertically stacked DataFrame, named `vstackedDf` .

## Recipe 5-6\. Horizontally Stack Two DataFrames

### Problem

You want to horizontally stack two DataFrames.

### Solution

Horizontal stacking is less frequent in day-to-day work, but this does not decrease the importance of it. Figure [5-5](#469054_1_En_5_Chapter.xhtml#Fig5) shows horizontal stacking of the tables called `dfOne` and `dfTwo`.

![../images/469054_1_En_5_Chapter/469054_1_En_5_Fig5_HTML.jpg](images/469054_1_En_5_Chapter/469054_1_En_5_Fig5_HTML.jpg)

Figure 5-5

Horizontally stacking DataFrames

We can observe from Figure [5-5](#469054_1_En_5_Chapter.xhtml#Fig5) that horizontal stacking means putting DataFrames side by side. It is not easy in PySparkSQL, as there is no dedicated API for this task.

Since there is no dedicated API for horizontal stacking, we have to use other API to get the job done. How do we perform horizontal stacking? One way is to add a new column of row numbers in each DataFrame. After adding this new column of row numbers, we can perform an inner join to get the required DataFrames vertically stacked.

We have to perform the following:

*   We have to read two tables, called `firsthorizontable` and `secondhorizontable`, from the PostgreSQL database and create DataFrames.

*   We have to perform horizontal stacking of these newly created DataFrames.

We can observe our tables in PostgreSQL using the following commands.

```
pysparksqldb=# select * from firsthorizontable ;
```

Here is the output:

```
iv1|  iv2|  iv3
-----+-----+-----
9|11.43|10.25
10.26| 8.35| 9.94
9.84| 9.28| 9.22
11.77|10.18|11.02
10.23|10.19| 8.17
(5 rows)
pysparksqldb=# select * from secondhorizontable;
```

Here is the output:

```
iv4|  iv5|  iv6
-----+-----+-----
11|12.64|12.18
12.26|10.84|12.19
11.84|13.43| 11.6
13.77|10.35|13.48
12.23|11.28|12.25
(5 rows)
```

### How It Works

### Note

In order to connect with the PostgreSQL database, we have to start a PySpark shell with the following options:

`$pyspark --driver-class-path  ~/.ivy2/jars/org.postgresql_postgresql-42.2.4.jar  --packages org.postgresql:postgresql:42.2.4`

#### Step 5-6-1\. Reading Tables from PostgreSQL and Creating DataFrames

We have to read two tables from the PostgreSQL database. Let’s read these one by one.

```
In [1]: dbURL = "jdbc:postgresql://localhost/pysparksqldb?user=postgres&password=""
In [2]: horizontalDfOne = spark.read.format("jdbc").options(url = dbURL, database ='pysparksqldb', dbtable ='firsthorizontable').load();
In [3]: horizontalDfOne.show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.26| 8.35| 9.94|
| 9.84| 9.28| 9.22|
|11.77|10.18|11.02|
|10.23|10.19| 8.17|
+-----+-----+-----+
In [4]: horizontalDfTwo = spark.read.format("jdbc").options(url = dbURL, database ='pysparksqldb', dbtable ='secondhorizontable').load();
In [5]: horizontalDfTwo.show()
```

Here is the output:

```
+-----+-----+-----+
|  iv4|  iv5|  iv6|
+-----+-----+-----+
| 11.0|12.64|12.18|
|12.26|10.84|12.19|
|11.84|13.43| 11.6|
|13.77|10.35|13.48|
|12.23|11.28|12.25|
+-----+-----+-----+
```

#### Step 5-6-2\. Performing Horizontal Stacking of Newly Created DataFrames

After creating the DataFrames, we have to add a new column to both. Both DataFrames need to have a new column that shows the integer sequence. We can create this new column using the `monotonically_increasing_id()` function. We can get this function from the `pyspark.sql.functions` submodule. Let’s import it.

```
In [5]: from pyspark.sql.functions import monotonically_increasing_id
```

In the following line of code, we are going to add a new column that contains a sequence of integers, starting from zero.

```
In [6]: horizontalDfOne = horizontalDfOne.withColumn("id", monotonically_increasing_id() )
In [7]: horizontalDfOne.show()
```

Here is the output:

```
+-----+-----+-----+--+
|  iv1|  iv2|  iv3|id|
+-----+-----+-----+--+
| 9.0 |11.43|10.25| 0|
|10.26| 8.35| 9.94| 1|
| 9.84| 9.28| 9.22| 2|
|11.77|10.18|11.02| 3|
|10.23|10.19| 8.17| 4|
+-----+-----+-----+--+
```

We have successfully added the new column of integer sequence to the `horizontalDfOne` DataFrame. The following line of code will add the same column to the `horizontalDfTwo` DataFrame .

```
In [8]: horizontalDfTwo = horizontalDfTwo.withColumn( "id",  monotonically_increasing_id() )
In [9]: horizontalDfTwo.show()
```

Here is the output:

```
+-----+-----+-----+--+
|  iv4|  iv5|  iv6|id|
+-----+-----+-----+--+
| 11.0|12.64|12.18| 0|
|12.26|10.84|12.19| 1|
|11.84|13.43| 11.6| 2|
|13.77|10.35|13.48| 3|
|12.23|11.28|12.25| 4|
+-----+-----+-----+--+
```

We are now going to perform an inner join on `horizontalDfOne` and `horizontalDfTwo`. An inner join is performed on the `id` column.

```
In [10]: hStackedDf = horizontalDfOne.join(horizontalDfTwo, horizontalDfOne.id == horizontalDfTwo.id, how="inner")
In [11]: hStackedDf.show()
```

Here is the output:

```
+-----+-----+-----+--+-----+-----+-----+--+
|  iv1|  iv2|  iv3|id|  iv4|  iv5|  iv6|id|
+-----+-----+-----+--+-----+-----+-----+--+
|  9.0|11.43|10.25| 0| 11.0|12.64|12.18| 0|
|10.26| 8.35| 9.94| 1|12.26|10.84|12.19| 1|
|11.77|10.18|11.02| 3|13.77|10.35|13.48| 3|
| 9.84| 9.28| 9.22| 2|11.84|13.43| 11.6| 2|
|10.23|10.19| 8.17| 4|12.23|11.28|12.25| 4|
+-----+-----+-----+--+-----+-----+-----+--+
```

After the inner join, we have our two DataFrames horizontally stacked and the results stored in `hStackedDf`. We are not in need of the `id` column. Therefore, we can drop it.

```
In [12]: hStackedDf = hStackedDf.drop("id")
In [13]: hStackedDf.show()
```

Here is the output:

```
+-----+-----+-----+-----+-----+-----+
|  iv1|  iv2|  iv3|  iv4|  iv5|  iv6|
+-----+-----+-----+-----+-----+-----+
|  9.0|11.43|10.25| 11.0|12.64|12.18|
|10.26| 8.35| 9.94|12.26|10.84|12.19|
|11.77|10.18|11.02|13.77|10.35|13.48|
| 9.84| 9.28| 9.22|11.84|13.43| 11.6|
|10.23|10.19| 8.17|12.23|11.28|12.25|
+-----+-----+-----+-----+-----+-----+
```

And finally we have our required result.

## Recipe 5-7\. Perform Missing Value Imputation

### Problem

You want to perform missing value imputation in a DataFrame.

### Solution

For the data scientist, missing values are inevitable. PySparkSQL provides tools to handle missing data in DataFrames. Two important functions that deal with missing or null values are `dropna()` and `fillna()`.

In Figure [5-6](#469054_1_En_5_Chapter.xhtml#Fig6), the contents of the DataFrame are displayed. We can observe that there are two missing values in row four and is one missing value in row two.

![../images/469054_1_En_5_Chapter/469054_1_En_5_Fig6_HTML.jpg](images/469054_1_En_5_Chapter/469054_1_En_5_Fig6_HTML.jpg)

Figure 5-6

DataFrame with missing values

The `dropna()` function can remove rows that contain null data. It takes three arguments. The first argument is `how`, and it can take two values—`any` or `all`. If the value of `how` is `all`, a row will be dropped only if all the values of the row are `null`. If the value of `how` is `any`, the row will be dropped if any of its values are null.

The second argument of the `dropna()` function is called `thresh`. The default value for `thresh` is `None`. It takes an integer value. The `thresh` argument overwrites the first argument, `how`. If `thresh` is set to the integer `n`, all rows where the number of non-null values is less than `n` will be dropped.

The last argument of the `dropna()` function is `subset`. This is an optional name of columns to be considered.

The second important function for dealing with null values is `fillna()`. The first argument is a value. The `fillna()` function will replace any null value with the `value` argument .

### How It Works

#### Step 5-7-1\. Reading Data from MongoDB

We have a table called `missingData` in the `pysparksqlbook` database of MongoDB. We have to start the PySpark shell using a MongoDB driver. The following line of code reads the data from MongoDB.

```
In [1]: missingDf = spark.read.format(   "com.mongodb.spark.sql.DefaultSource"  ).option("uri" , "mongodb://127.0.0.1/pysparksqlbook.missingData" ).load()
In [2]: missingDf.show()
```

Here is the output:

```
+--------------------+-----+-----+-----+
|                 _id|  iv1|  iv2|  iv3|
+--------------------+-----+-----+-----+
|[5ba7e6c059acc01f...|  9.0|11.43|10.25|
|[5ba7e6c059acc01f...|10.23|     | 8.17|
|[5ba7e6c059acc01f...|10.26| 8.35| 9.94|
|[5ba7e6c059acc01f...| 9.84|     |     |
|[5ba7e6c059acc01f...|11.77|10.18|11.02|
+--------------------+-----+-----+-----+
```

We do not need the `_id` column, so we can drop it.

```
In [3]: missingDf = missingDf.drop("_id")
In [4]: missingDf.show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.23|     | 8.17|
|10.26| 8.35| 9.94|
| 9.84|     |     |
|11.77|10.18|11.02|
+-----+-----+-----+
```

We have dropped the `_id` column. But, in the `missingDf` DataFrame, we can observe that the missing values are not shown as null, but rather as empty strings. We can verify this using the `printSchema()` function.

```
In [5]: missingDf.printSchema()
```

Here is the output:

```
root
|-- iv1: double (nullable = true)
|-- iv2: string (nullable = true)
|-- iv3: string (nullable = true)
```

We can see that PySparkSQL has interpreted columns `iv2` and `iv3` as string data. Let’s typecast the datatype of these columns to `DoubleType()`. We know that all the datatypes are defined in the `pyspark.sql.types` submodule. Let’s import `DoubleType` from the `pyspark.sql.types` submodule .

```
In [6]: from pyspark.sql.types import DoubleType
```

We can typecast the datatype using the `cast()` function inside the `withColumn()` function, as shown in this code.

```
In [7]: missingDf =  missingDf.withColumn("iv2", missingDf.iv2.cast(DoubleType())).withColumn("iv3", missingDf.iv3.cast(DoubleType()))
In [8]: missingDf.show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.23| null| 8.17|
|10.26| 8.35| 9.94|
| 9.84| null| null|
|11.77|10.18|11.02|
+-----+-----+-----+
```

After typecasting the data, we can observe nulls in the DataFrame. We can also verify the schema of the DataFrame.

```
In [9]: missingDf.printSchema()
```

Here is the output:

```
root
|-- iv1: double (nullable = true)
|-- iv2: double (nullable = true)
|-- iv3: double (nullable = true)
```

#### Step 5-7-2\. Dropping the Rows that Have Null Values

We are going to drop the rows that contain null values. We can drop those rows using the `dropna()` function. We are going to set the value of the `how` argument to `any`. That way, the data in rows two and four will be dropped.

```
In [10]: missingDf.dropna(how ='any').show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.26| 8.35| 9.94|
|11.77|10.18|11.02|
+-----+-----+-----+
```

Since all the values are not null, the `all` value of `how` won’t affect the DataFrame.

```
In [11]: missingDf.dropna(how ='all').show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.23| null| 8.17|
|10.26| 8.35| 9.94|
| 9.84| null| null|
|11.77|10.18|11.02|
+-----+-----+-----+
```

#### Step 5-7-3\. Dropping Rows that Have Null Values Using the thresh Argument

If the `thresh` value is set to `2`, any row containing less than two non-null values will be dropped. Only the fourth column has fewer than two non-null values (it has only one), so it is the only row that will be dropped.

```
In [12]: missingDf.dropna(how ='all',thresh=2).show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.23| null| 8.17|
|10.26| 8.35| 9.94|
|11.77|10.18|11.02|
+-----+-----+-----+
```

We can observe that in output, only the fourth row was dropped. Now let’s change the value of thresh from `2` to `3`. In that case, rows two and four both are dropped.

```
In [13]: missingDf.dropna(how ='all',thresh=3).show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.26| 8.35| 9.94|
|11.77|10.18|11.02|
+-----+-----+-----+
```

#### Step 5-7-4\. Filling in the Missing Value with Some Number

In this step, we are going to replace null values with zeros using the `fillna()` function.

```
In [14]: missingDf.fillna(value=0).show()
```

Here is the output:

```
+-----+-----+-----+
|  iv1|  iv2|  iv3|
+-----+-----+-----+
|  9.0|11.43|10.25|
|10.23|  0.0| 8.17|
|10.26| 8.35| 9.94|
| 9.84|  0.0|  0.0|
|11.77|10.18|11.02|
+-----+-----+-----+
```

# 6. SQL, NoSQL, and PySparkSQL

In this chapter, we will look into various Spark SQL recipes that come in handy when you have to apply SQL-like queries to the data. One of the specialties of Apache Spark is the way in which it lets the user apply data-wrangling methods in a programmatic manner and as ANSI SQL-like methods. For readers who are from pure SQL backgrounds, with a little exposure to programmatic data manipulation, these SQLs are one stop shop. Almost all of the Spark programmatic APIs can be applied to data using Spark SQLs. Also these SQLs are in ANSI standard, which enables anyone currently on SQL technologies to readily start working on Apache Spark-based Big Data projects.

Once we get the DataFrame created, we can directly start applying the SQLs onto the DataFrame object and be able to manipulate the data as we need to. In this chapter, starting from simple SQLs, we look into various SQL applications in Apache Spark.

We are going to learn the following recipes, which will take you step-by-step from a beginner to an advanced Spark SQL user. We recommend you practice each of these to get the best out of this chapter.

*   Recipe 6-1\. Create a DataFrame from a CSV file

*   Recipe 6-2\. Create a temp table from a DataFrame

*   Recipe 6-3\. Create a simple SQL from a DataFrame

*   Recipe 6-4\. Apply Spark UDF methods on Spark SQL

*   Recipe 6-5\. Create a new PySpark UDF

*   Recipe 6-6\. Join two DataFrames using SQL

*   Recipe 6-7\. Join multiple DataFrames using SQL

## Recipe 6-1\. Create a DataFrame from a CSV File

### Problem

You want to create a DataFrame from a CSV file.

### Solution

For the sake of simplicity, we are going to use very simple data so that we can focus on the logic and Spark APIs without any questions or distractions from the data. We are using the well known student data shown in Table [6-1](#469054_1_En_6_Chapter.xhtml#Tab1). There are three columns in this data—`studentid`*,* `name`*,* and `gender`.

Table 6-1

*Sample Data*

| studentId | name | gender |
| --- | --- | --- |
| si1 | Robin | M |
| si2 | Maria | F |
| si3 | Julie | F |
| si4 | Bob | M |
| si6 | William | M |

The assignment is to create a DataFrame from this data.

Although you would have already performed the following steps in the previous chapters to create a DataFrame from the CSV, we start with this for continuity purposes. Also, anyone with a pure SQL background starting directly with this chapter will be lost without this step. So let’s get started:

```
>>> studentDf = spark.read.csv('studentData.csv',
...                           header=True, inferSchema=True)
>>> studentDf.show()
+---------+-------+-------+
|studentId|   name| gender|
+---------+-------+-------+
|      si1|  Robin|      M|
|      si2|  Maria|      F|
|      si3|  Julie|      F|
|      si4|    Bob|      M|
|      si6|William|      M|
+---------+-------+-------+
```

We have successfully converted the data into a Spark DataFrame. You might notice that this is very simple. Yes, Spark’s abstractions API makes it very simple for users to load data into a DataFrame.

### How It Works

Let’s do a deep dive into the code that created the DataFrame. `spark.read.csv` is the Spark API that provides you with the feature to read CSV files. It returns a DataFrame as the output. You can also see there are two more arguments provided along with the filename—`inferSchema` and `header`. These parameters tell Spark that the schema that needs to be tied to the DataFrame should be obtained from the header row.

To make sure we create the correct schema as expected, we can use the `printSchema()` method of DataFrame as shown here.

```
>>> studentDf.printSchema()
root
|-- studentId: string (nullable = true)
|-- name: string (nullable = true)
|-- gender: string (nullable = true)
```

Executing `studentDf.printSchema()` will provide us with the column names of the DataFrame.

#### Step 6-1-1\. Creating a DataFrame from JSON

Very often in real-time projects we get data files in the JSON format. JSON is one of the highly used data formats. So let’s create a DataFrame from a JSON file. For the sake of simplicity, we use the same students data in the JSON format to create the DataFrame. The JSON-formatted data is given here. The attributes in a JSON data file will be placed into individual columns in the DataFrame. Each document in a DataFrame will be represented as a row in the DataFrame. It is very easy to create manual errors when JSON data is created manually. If there are any errors, there will not be pinpoint error messages given out. In that case, it becomes very hard to debug such data issues. To avoid all such scenarios it is always best to create the JSON data programmatically.

Figure [6-1](#469054_1_En_6_Chapter.xhtml#Fig1) shows the students data in JSON format. There are five documents, each containing the same set of attributes.

![../images/469054_1_En_6_Chapter/469054_1_En_6_Fig1_HTML.jpg](images/469054_1_En_6_Chapter/469054_1_En_6_Fig1_HTML.jpg)

Figure 6-1

Students data in JSON format

Let’s complete the creation of the DataFrame from the JSON file:

```
>>> studentDf_json = spark.read.csv('students.json')
>>> studentDf.printSchema()
```

Here is the output showing the schema of the DataFrame. Note that this is the exact schema we got when we created the DataFrame from the CSV file.

```
root
|-- studentId: string (nullable = true)
|-- name: string (nullable = true)
|-- gender: string (nullable = true)
```

Let’s see the contents of the DataFrame using the `show` method.

```
>>> studentDf.show()
```

Here is the output:

```
+---------+-------+--------+
|studentId|   name|  gender|
+---------+-------+--------+
|      si1|  Robin|       M|
|      si2|  Maria|       F|
|      si3|  Julie|       F|
|      si4|    Bob|       M|
|      si6|William|       M|
+---------+-------+--------+
```

#### Step 6-1-2\. Creating a DataFrame from Parquet

So far we have looked into CSV and JSON files. When it comes to the Big Data space where huge volumes of data are processed, the Parquet format is rated very well because of its support for columnar storage. Parquet comes with a highly efficient compression mechanism as well as encoding schemes. Parquet is known for improving query performance.

Although we saw the CSV and JSON files, we will not be showing the Parquet file since it is not human friendly to read. So let’s get straight into creating the DataFrame from the Parquet file.

Here we have a Parquet file named `studentsData.parquet` that contains the same students data that was loaded into the CSV and JSON formats.

```
>>> studentdf_parquet = spark.read.parquet('studentsData.parquet')
```

Let’s see the schema of the DataFrame.

```
>>> studentdf_parquet.printSchema()
```

Here is the output. You can see that this is exactly the same schema we saw for the CSV and JSON formats.

```
root
|-- studentId: string (nullable = true)
|-- name: string (nullable = true)
|-- gender: string (nullable = true)
```

Let’s see the contents of the DataFrame using the `show` method.

```
>>> studentdf_parquet.show()
```

Here is the output:

```
+---------+-------+------+
|studentId|   name|gender|
+---------+-------+------+
|      si1|  Robin|     M|
|      si2|  Maria|     F|
|      si3|  Julie|     F|
|      si4|    Bob|     M|
|      si6|William|     M|
+---------+-------+------+
```

At this stage, we have the DataFrame that is very essential to apply the SQL queries. The beauty of Spark is to realize how it can abstract data from various formats into its one DataFrame abstraction. This also means that the data munging applied to the DataFrame is agnostic to the file format. For example, let’s assume you have completed your data programming for the JSON formatted data and you want to change it to Parquet. There is no code change you need to do except for changing the data format API while reading. Other than that, the program written for JSON remains the same for Parquet.

## Recipe 6-2\. Create a Temp View from a DataFrame

### Problem

While creating a DataFrame is the first step in starting to use Spark SQL, the second step is to register the created DataFrame as a temporary table in Spark’s SparkSession. For those who are wondering what a SparkSession is, it is the entry point for working with structured data such as rows and columns. Spark provides an API called `createOrReplaceTempView` that lets you register your DataFrame as queryable entities.

### Solution

This temp view will be represented as an in-memory entity that can be used in analytics and other repetitive operations on the same data.

### How It Works

Let’s create the temporary view.

```
>>> studentDf.createOrReplaceTempView("Students")
```

Executing this statement will create a temporary view called `Students`. Let’s go ahead and verify this.

```
>>> spark.tableNames()
```

Here is the output:

```
['students']
```

Here you can verify that the `students` view has been registered with SparkSession and is now readily available to be used within SQL queries. You might be wondering how long this view will be available for use. For example, if we exit the Spark Session and log in again, will this view still be available and so on. They will not, as these views are scoped only for the session in which they are created. When we log off from the Spark session and log in again, the `students` view we created will not be available.

Let’s verify that here:

```
>>> quit()
```

We quit from the session and log in again.

```
>>> from pyspark import SQLContext
>>> sqlContext = SQLContext(sc)
```

With these statements, we are importing the `SQLContext` and then instantiating `SQLContext` using SparkContext that is available by default using the `sc` variable name.

```
>>> sqlContext.tableNames()
```

Here is the output that shows that there are no existing views in the session.

```
[]
```

## Recipe 6-3\. Create a Simple SQL from a DataFrame

### Problem

So far we have been setting up the base to start using Spark SQL. We created a DataFrame from data files and registered that DataFrame as a temporary view. Now we use the temporary view to apply our first simple SQL query.

### Solution

We have to create a simple query. A simple query for anyone would mean `select * from table`*.* Let’s do that here.

```
>>> outputDf = spark.sql("select * from students")
>>> outputDf.show()
```

Here is the output:

```
+---------+-------+------+
|studentId|   name|gender|
+---------+-------+------+
|      si1|  Robin|     M|
|      si2|  Maria|     F|
|      si3|  Julie|     F|
|      si4|    Bob|     M|
|      si6|William|     M|
+---------+-------+------+
```

Notice that we are able to query on the data using a SQL query. The output is the same as what we saw earlier.

### How It Works

#### Step 6-3-1\. Using Column Names in Spark SQL

Now that we applied a simple SQL query, let’s start using the column names inside the query.

Let’s look at the column names that are available inside the `students` view. Recall that the `printSchema` method of the DataFrame can be used to display the schema. For SQL lovers, there is another way to view and analyze details.

Spark provides `Describe`, which can be used to see the table/view’s column, datatypes, and any comments, which usually indicate whether the column accepts null.

Let’s apply the `Describe` keyword on the `students` view.

```
>>> spark.sql("Describe students").show()
```

Here is the output:

```
+---------+---------+-------+
| col_name|data_type|comment|
+---------+---------+-------+
|studentId|   string|   null|
|     name|   string|   null|
|   gender|   string|   null|
+---------+---------+-------+
```

From the output, we can see that the column names are the same as the names we saw from the `printSchema` method. The `Describe` option provides a SQL style way of getting the table schema.

Let’s go ahead and apply a query using these columns. Let’s get a student report with name and gender only, since for reporting usually we will not require the ID.

The query we are going to run will be “Get a report of all the students with their name and gender.”

```
>>> spark.sql("select name,gender from students").show().
```

Here is the output:

```
+-------+------+
|   name|gender|
+-------+------+
|  Robin|     M|
|  Maria|     F|
|  Julie|     F|
|    Bob|     M|
|William|     M|
+-------+------+
```

As per the output, we can see that only the `name` and `gender` columns are displayed.

#### Step 6-3-2\. Creating an Alias for Column Names

From a reporting perspective, we want the report to provide formatted column names and easily understandable column names. Let’s assume that our report needs these formats:

```
"name" should be displayed as "Name" with N capitals.
gender should be displayed as "Sex"
```

To do this, Spark lets us create aliases that can be used to display the column names the way we need them to be.

```
>>> spark.sql("select name as Name,gender as Sex from students").show()
```

Here is the output:

```
+-------+----+
|   Name| Sex|
+-------+----+
|  Robin|   M|
|  Maria|   F|
|  Julie|   F|
|    Bob|   M|
|William|   M|
+-------+----+
```

Upon analyzing the output, we see that the column names are displayed as expected. To be able to create an alias, we need to keep the alias name next to the original column name. The keyword `as` is optional. This aliasing concept will be very helpful in complex queries, which we will see later in this chapter.

#### Step 6-3-3\. Filtering Data Using a Where Clause

In this recipe, we are going to apply filters on the data to select only female students, ones with gender set to `F`. The `where` clause is used to apply filtering on the column values.

```
>>> spark.sql("select name ,gender from students where gender = 'F'").show()
```

Let’s see the output here:

```
+-------+-------+
|   name| gender|
+-------+-------+
|  Maria|      F|
|  Julie|      F|
+-------+-------+
```

As you can observe from the output, we get only the female students. At this moment it is important to note that Spark is very efficient in handling filter criteria. If there are multiple filter criteria applied in the same DataFrame during the course of a program at various points, Spark knows to combine that filter criteria so it can apply all of it at once. The reason for that is internally Spark will scan through each of the rows to identify the matching rows according to the filter criteria. When some of these criteria are applied on the same dataset multiple times, it becomes very costly. Spark uses a special Query Optimizer called Catalyst to be able to understand such filter criteria in this process.

While we have seen examples that work well and fine, there are some pitfalls for the readers who come from an RDBMS SQL background. So let’s look at those pitfalls next.

#### Step 6-3-4\. Filtering on Case-Sensitive Column Values

In the previous step, the query filtered on students that were females. The female gender is represented by the column value `F`, capital to be noted. By chance if we are filtering based on `f`, then there will not be any output from the query since there are no values with `f` in the gender column. Let’s go ahead and filter based on the `f` criteria .

```
>>> spark.sql("select name ,gender from Students where Gender = 'f'").show()
```

Here is the output where we can observe that there are no records printed:

```
+----+------+
|name|gender|
+----+------+
+----+------+
```

There will be scenarios when we don’t know the column value’s case (whether the value is uppercase or lowercase). In those scenarios, we need to apply functions on the column as follows.

Let’s go ahead and use the function `lower`, which converts the value into lowercase before doing the comparison. In Spark SQL, this is called a UDF, or user-defined function. Spark provides a variety of predefined UDFs. `lower` is one such UDF that converts values from uppercase to lowercase. Let’s use the `lower` function and see the results.

```
>>> spark.sql("select name ,gender from Students where lower(gender) = 'f'").show()
```

Here is the output where you can see the results with the female students:

```
+-----+------+
| name|gender|
+-----+------+
|Maria|     F|
|Julie|     F|
+-----+------+
```

#### Step 6-3-5\. Trying to Use Alias Columns in a Where Clause

Generally, many RDBMS databases allow us to use alias names in the `where` clause, `group by` clause, and other such clauses. But if we try to use the same in Spark SQL, we will see a strange error. The output says that it cannot resolve the column name. This is because Spark SQL does not allow using alias column names in the same SQL.

Let’s use an alias name in the SQL and look at the output:

```
>>> spark.sql("select name ,gender Sex from Students where Lower(Sex) = 'f'").show()
```

Here is the output:

```
raise AnalysisException(s.split(': ', 1)[1], stackTrace)
pyspark.sql.utils.AnalysisException: "cannot resolve '`Sex`' given input columns: [students.studentId, students.name, students.gender]; line 1 pos 44;\n'Project ['name, 'gender AS Sex#163]\n +- 'Filter ('Sex = f)\n +- SubqueryAlias students\n +- Relation[studentId#0,name#1,gender#2] parquet\n"
```

While we might see a long stack of exceptions, do not get scared by this. At the very end, notice the friendly message mentioning that the given column cannot be resolved. This is to say that Spark SQL does not recognize alias columns in the SQL.

## Recipe 6-4\. Apply Spark UDF Methods on Spark SQL

### Problem

You want to use Spark UDF functions in Spark SQLs.

### Solution

User-defined functions (UDFs) are special functions that help in transforming data from one representation to another. In the previous section, we worked with the `lower` UDF to convert characters into lowercase. Other similar examples are converting date strings from one format to another, and converting a character into its detailed value, such as M into Male and F into Female.

### How It Works

Let’s apply a UDF to convert a date from one format to another.

The data used in this problem contains an additional column joining the date of birth of each student. Let’s load the data and look at the contents.

```
>>> studentDf = spark.read.csv('studentData_dateofbirth.csv',header=True, inferSchema=True)
>>> studentDf.printSchema()
```

Let’s print the schema of the new data, `studentData_dateofbirth.csv`.

```
root
|-- studentId: string (nullable = true)
|-- name: string (nullable = true)
|-- gender: string (nullable = true)
|-- dateofbirth: string (nullable = true)
```

The new data is loaded and, on printing the schema, we get the output shown in Figure [6-2](#469054_1_En_6_Chapter.xhtml#Fig2). Note that there is an extra column now named `dateofbirth`.

![../images/469054_1_En_6_Chapter/469054_1_En_6_Fig2_HTML.jpg](images/469054_1_En_6_Chapter/469054_1_En_6_Fig2_HTML.jpg)

Figure 6-2

Students data with date of birth column added

We have introduced a new column called `dateofbirth` that lets us apply UDFs on this column. Spark by default provides a lot of date functions that can be applied on date values, such as converting a string value into a date, showing the difference between two dates, adding and subtracting dates, and so on. All these functionalities are available in Spark as user-defined functions.

#### Step 6-4-1\. Representing a String Value as a Date Value

We need to view the default datatype of the `dateofbirth` column in the view. Before seeing that, we need to create a temporary view of the updated data.

Let’s go ahead and do that:

```
>>> studentDf.createOrReplaceTempView("Students")
```

With this, we created a `Students` view in Spark.

Let’s look at the columns and their respective datatypes in the newly created table.

```
>>> spark.sql("Describe students").show()
```

Here is the output:

```
+-----------+---------+-------+
|   col_name|data_type|comment|
+-----------+---------+-------+
|  studentId|   string|   null|
|       name|   string|   null|
|     gender|   string|   null|
|dateofbirth|   string|   null|
+-----------+---------+-------+
```

Here we can notice that the `dateofbirth` column is represented with the `string` datatype. This is because Spark considers the contents of the CSV file as a `string` datatype. Let’s use Spark’s `Date` UDFs to convert this column to the `Date` datatype .

```
>>> spark.sql("select studentid, name, gender, to_date(dateofbirth) from students").show()
```

Here is the output:

```
+----------+-------+-------+--------------------------------+
| studentid|   name| gender| to_date(students.`dateofbirth`)|
+----------+-------+-------+--------------------------------+
|       si1|  Robin|      M|                      1981-12-13|
|       si2|  Maria|      F|                      1986-06-06|
|       si3|  Julie|      F|                      1988-09-05|
|       si4|    Bob|      M|                      1987-05-04|
|       si6|William|      M|                      1980-11-12|
+----------+-------+-------+--------------------------------+
```

Notice from the output that the `dateofbirth` column was given as the input to the `to_date` UDF, which returns the value with `Date` as the datatypes. Let’s verify the datatype of that column.

```
>>> studentDf_dob = spark.sql("select studentid, name, gender, to_date(dateofbirth) from students")
>>> studentDf_dob.createOrReplaceTempView("StudentsDob")
```

Here we are creating another table that will contain the converted `dateofbirth` column.

Let’s view the newly created table and its schema using the `describe` keyword.

```
>>> spark.sql("Describe studentsDob").show()
```

Here is the output:

```
+--------------------+---------+-------+
|            col_name|data_type|comment|
+--------------------+---------+-------+
|           studentid|   string|   null|
|                name|   string|   null|
|              gender|   string|   null|
|to_date(students....|     date|   null|
+--------------------+---------+-------+
```

You can clearly notice that there is a column with the datatype set to `date`. But we still do not have a clear column name that can be easily understood. Let’s use aliasing to create a better column name.

Here we are following the same steps, except we are applying the aliasing to the `dateofbirth` column .

```
>>> studentDf_dob = spark.sql("select studentid, name, gender, to_date(dateofbirth) dateOfBirth from students")
>>> studentDf_dob.createOrReplaceTempView("StudentsDob")
>>> spark.sql("Describe studentsDob").show()
```

Here is the output:

```
+-----------+---------+-------+
|   col_name|data_type|comment|
+-----------+---------+-------+
|  studentid|   string|   null|
|       name|   string|   null|
|     gender|   string|   null|
|dateOfBirth|     date|   null|
+-----------+---------+-------+
```

The schema of the newly created table is now easy to use with the well-defined column name `dateOfBirth` .

#### Step 6-4-2\. Using Date Functions for Various Date Manipulations

In this step we are going to separate the date, month, and year values using various `Date` user-defined functions. For ease of understanding, we picked up examples that every reader will be able to appreciate.

Let’s start by defining the SQL that separates the individual values of a `Date` column.

```
>>> spark.sql("select dateofbirth,dayofmonth(dateofbirth) day, month(dateofbirth) month, year(dateofbirth) year from  studentsdob").show()
```

The previous query extracts the individual values as shown here:

```
Date using dayofmonth
Month using month
Year using year
```

Here is the output:

```
+-----------+---+-----+----+
|dateofbirth|day|month|year|
+-----------+---+-----+----+
| 1981-12-13| 13|   12|1981|
| 1986-06-06|  6|    6|1986|
| 1988-09-05|  5|    9|1988|
| 1987-05-04|  4|    5|1987|
| 1980-11-12| 12|   11|1980|
+-----------+---+-----+----+
```

It can be clearly seen that these user-defined functions are very valuable since they enable us to operate on individual columns and process the values.

User-defined functions are nothing but internal programs defined inside Spark using the Java, Scala, or Python languages. These functions take the given column as input values and apply the functionality of each row in that table. For example, the `year` UDF takes the `dateofbirth` column value, gets the year part of the date, and returns the `year` value of the date. This will be applied to each row in the table. Similarly, `month` and `dayofmonth` are UDFs defined internally within Spark SQL and they return the `month` and `date` values, respectively.

In this section, we saw many date-related UDFs. Similar to these, there are multiple string-related functions as well as statistical functions. All these UDFs can be used in Spark SQL queries.

You might wonder what if you want to apply functions that need to aggregate column values.

## Recipe 6-5\. Create a New PySpark UDF

### Problem

You need to create a new, custom UDF. The `gender` column of the `Students` data contains an `M` or `F` to represent male or female, respectively. You need to create a UDF that will return `Male` or `Female` as values instead of `M` or `F`.

### Solution

Let’s begin creating the UDF. It is very interesting to add features that can be used in SQL queries.

### How It Works

#### Step 6-5-1\. Creating a Regular Python Function That Accepts a Value and Returns the Required Value

While creating a Python function requires a little bit of Python knowledge, it is not very complicated. So let’s create one. It is very important to provide a meaningful name for the function.

```
>>> def genderCodeToValue(code):
...     return('FEMALE' if code == 'F' else 'MALE' if code == 'M' else 'NA')
```

Here we have created a simple function that accepts a code and return its respective value. If the code is not `M` or `F`, it returns `NA` as the value. I have kept it very simple for you to understand the concept.

Let’s test the function by providing some values.

Here are the test results.

```
>>> print(genderCodeToValue('M'))
Testing with code as 'M'
```

Here is the output:

```
MALE
Testing with code 'F'.
>>> print(genderCodeToValue('F'))
```

Here is the output:

```
FEMALE
Testing with code 'K as value not either 'M' or 'F'
>>> print(genderCodeToValue('K'))
```

Here is the output:

```
NA
```

So we have successfully tested this simple function. It is always good to test your functions upfront before applying them on the dataset. This is called *unit testing* and it helps you identify and fix errors in your code immediately after you write it. Imagine if you were using the code directly on a huge set of data; you may not be able to see the errors or their root causes. Especially since Spark is typically used on huge volumes of data, errors in UDFs are very hard to find. It is very important to test the Python function with all possible inputs before trying it on the data.

#### Step 6-5-2\. Registering the Python Function in a Spark SQL Session

After creating the Python function, the nest step is to register the function as a UDF in the Spark SQL. This is achieved as follows.

```
>>> from pyspark.sql.functions import udf
>>> from pyspark.sql.types import StringType
```

Before registering the `genderCodeToValue` as a UDF, you need to import these types into Python. First you need to import the `udf` function from the `pyspark.sql.functions` package and then you need to import `StringType` from the `pyspark.sql.types` package. `StringType` is needed for Spark to verify that the SQL is intact with the return type of the user-defined function.

Let’s now register the UDF.

```
>>> spark.udf.register("genderCodeToValue", genderCodeToValue, StringType())
```

Here is the output indicating that the UDF has been successfully registered with Spark SQL:

```
This is the easiest way to register UDFs.
This method of registration uses the `spark.udf.register`
function. Let’s look at each of the arguments passed in this
function.
The first one is `genderCodeToValue`. It
is the name of UDF. This is the name that will be used in the Spark SQL
to call this function.
The second one is `genderCodeToValue`.
This is the method we just created that implements the required
functionality. Also note that this is a reference to the method. In this
case, the method is passed as a reference. It is very important at this
point that any issues that will be encountered while this UDF is invoked
will be exposed only when the function is applied. That is when Spark
executes an action on the DataFrame. It is very important to make sure
that the function is tested for all the kinds of data that you will
operate on.
Also it is good practice to properly handle errors within UDFs. Many
times, UDFs are tested on smaller datasets and developers are happy that
the functions work perfectly. But in the typical Big Data world where
the data is error prone, it is very hard to identify when the flow
failed and what data caused the error. When millions of rows being
processed using UDFs are stopped because of one wrong issue that was not
handled properly, this is a nightmare in production environments.
The third function is `StringType()`*.*
This indicates that the UDF will return a `string` value. Spark
SQL uses this return type to properly validate and apply the UDF.

Step 6-5-3\. Calling the Registered UDF from PySpark
SQL
Now we are going to use the UDF  `genderCodeToValue`
, which we just created in Spark SQL. Let’s get started with
it.

Let’s assume we need to get a student report with the students’ names
and their expanded gender. Here is the query:

```
>>> spark.sql("select name,  genderCodeToValue(gender) from  studentsdob").show()
```

Here is the output:

```
+-------+-------------------------+
|   name|genderCodeToValue(gender)|
+-------+-------------------------+
|  Robin|                     MALE|
|  Maria|                   FEMALE|
|  Julie|                   FEMALE|
|    Bob|                     MALE|
|William|                     MALE|
+-------+-------------------------+
```

You can clearly see that the gender is shown with the output from the
UDF. The column heading is not very legible, so let’s correct that using
the following alias:

```
>>> spark.sql("select name as Name,  genderCodeToValue(gender) as Gender from  studentsdob").show()
```

Here is the output:

```
+-------+------+
|   Name|Gender|
+-------+------+
|  Robin|  MALE|
|  Maria|FEMALE|
|  Julie|FEMALE|
|    Bob|  MALE|
|William|  MALE|
+-------+------+
```

Note that we have cleaner output with the proper column alias names.
With this, we have successfully completed the assignment of creating a
custom UDF.

Recipe 6-6\. Join Two DataFrames Using SQL

Problem
You want to join two DataFrames using Spark SQL. Joining two or more
datasets is a very important step in any data transformation process.
Often as ETL developers we end up joining multiple tables to get the
desired output report. In a typical data warehouse, either the star
schema or the snowflake schema data modeling technique is followed. In
either of these data modeling techniques, lots of joins are involved.
With Big Data, it is preferred to have denormalized data models since
joining huge datasets will slow down the performance of the queries.
Nevertheless, we have to clearly understand how to join datasets and get
the reports.

Solution

Let’s start by joining two datasets. For this purpose, we are going to
introduce you to another student-based dataset, called `Subjects`. See Figure
[6-3](#469054_1_En_6_Chapter.xhtml#Fig3).

![../images/469054_1_En_6_Chapter/469054_1_En_6_Fig3_HTML.jpg](images/469054_1_En_6_Chapter/469054_1_En_6_Fig3_HTML.jpg)

Figure 6-3
The subjects dataset

We are going to get the marks obtained by the students in individual
subjects.
For that we need to first load this data as a DataFrame. At this
stage, since we have performed this multiple times, it would be a good
exercise if you could do this without referring to the following
code.

How It Works

Let’s get into the code. We are going to create a temporary view from
the `subjects`
data.

```
>>> subjectsDf = spark.read.csv('subjects.csv',header=True, inferSchema=True)
>>> subjectsDf.createOrReplaceTempView("subjects")
>>> spark.sql("select * from subjects").show()
```

Yes, it takes only these three lines of Spark code to load data from
CSV and be able to query from it using SQL. It is this specialty of
Spark that makes it great. Using any other language or tools, it will
take quite a lot of boilerplate code to be able to start querying this
data based on SQL. 

Here is the output:

```
+---------+-------+-----+
|studentId|subject|marks|
+---------+-------+-----+
|      si1| Python|   75|
|      si3|   Java|   76|
|      si1|   Java|   81|
|      si2| Python|   85|
|      si3|   Ruby|   72|
|      si4|    C++|   78|
|      si5|      C|   77|
|      si4| Python|   84|
|      si2|   Java|   83|
+---------+-------+-----+
```

Let’s all see the student data:

```
>>> spark.sql("select studentId,name,gender  from studentsdob").show()
+---------+-------+------+
|studentId|   name|gender|
+---------+-------+------+
|      si1|  Robin|     M|
|      si2|  Maria|     F|
|      si3|  Julie|     F|
|      si4|    Bob|     M|
|      si6|William|     M|
+---------+-------+------+
```

By observing both these datasets, note that `studentId` is the
column that can be used to join these two datasets. Let’s write the
query to join these datasets.

```
>>> spark.sql("select * from studentsdob st join subjects sb on (st.studentId = sb.studentId)").show()
```

Running this query produces the following results:

```
+---------+-----+------+-------------------+----------+-------+------+
|studentId| name|gender|        dateofbirth| studentId|subject| marks|
+---------+-----+------+-------------------+----------+-------+------+
|      si1|Robin|     M|1981-12-13 00:00:00|       si1|   Java|    81|
|      si1|Robin|     M|1981-12-13 00:00:00|       si1| Python|    75|
|      si2|Maria|     F|1986-06-06 00:00:00|       si2|   Java|    83|
|      si2|Maria|     F|1986-06-06 00:00:00|       si2| Python|    85|
|      si3|Julie|     F|1988-09-05 00:00:00|       si3|   Ruby|    72|
|      si3|Julie|     F|1988-09-05 00:00:00|       si3|   Java|    76|
|      si4|  Bob|     M|1987-05-04 00:00:00|       si4| Python|    84|
|      si4|  Bob|     M|1987-05-04 00:00:00|       si4|    C++|    78|
+---------+-----+------+-------------------+----------+-------+------+
```

Before getting into the observations, let’s try to understand the query.
You can observe from the query

```
select * from studentsdob st join subjects sb on (st.studentId = sb.studentId)
```

That there is a new keyword called `join` that’s used
between the `studentsdob` and `subjects` tables. We
are aliasing the tables with names. There is an `on` clause at the end
in which the common column is being compared. We will go into each of
these `join`
query specifics.

*   *Joins:* Tables are joined by applying the `join` keyword between
    the tables. There are multiple kinds of joins that Spark SQL supports.
    The most traditional and most used are the inner join, the left outer
    join, and the right outer join. By default, when the `join` keyword is
    provided, Spark performs an inner join. For an inner join, Spark
    compares the column values of each row of the tables on both the sides
    of the given column in the query. Only the rows that satisfy the
    criteria will be selected. The left outer join selects all the rows from
    the left table and rows that are not matching from the right side tables
    will be marked as null. The right outer join selects all rows from the
    right and rows not matching from the left table will be marked as
    null.

*   *Table alias:* Previously in this chapter we looked into
    aliasing columns. Similarly tables can also be provided an alias name.
    This will be helpful when the join column name is the same in both
    tables. The following query, which does not use alias names, gets an
    error due to its ambiguous column names: 

```
>>> spark.sql("select * from studentsdob  join subjects on (studentId = studentId)").show()
pyspark.sql.utils.AnalysisException: "Reference 'studentId' is ambiguous, could be: studentsdob.studentId, subjects.studentId.
```

You might see a huge stack trace before this error. But at the end, the
error says that the `studentid` is
ambiguous between the `studentsdob` and `subjects` tables.

*   *On clause:* Readers who are familiar with RDBMS SQLs will
    want to apply the `join` condition on the
    `where` clause
    rather on the `on` clause. While it
    is syntactically correct to compare the columns on the `where` clause, we will
    not get the correct results. This is because of how Spark achieves the
    join. So always apply the `join` condition to the
    `On` clause.

Now that we understand the query better, let’s analyze the results to
understand them better as well. The results look good and we have
obtained what we are looking for, but there are multiple observations we can make
from this data.

*   *Observation 1:* Some of the students are missing from
    this report. For example, the student named William does not appear in
    this output.

*   *Observation 2:* There was a labeled `studentId si5` that
    was available in the `subjects` dataset that
    is missing from this output.

Let’s explore these issues in more detail.

Observation 1
The reason we are not able to see William in the output is because
William, with `studentId si5`, is not
available in the `subjects` dataset.
Because of this, when Spark is applying the inner join, it doesn’t find
`studentid si5`,
so it drops it from the output. If the report needs to list all the
students whether they are available on the subjects dataset or not, we
need to use a left join or left outer join.

Let’s go ahead and use a left join to list the output. Here is the query
using a left join:

```
>>> spark.sql("select * from studentsdob st left join subjects sb on (st.studentId = sb.studentId)").show()
```

Here is the output:

```
+---------+-------+------+-------------------+---------+-------+-----+
|studentId|   name|gender|        dateofbirth|studentId|subject|marks|
+---------+-------+------+-------------------+---------+-------+-----+
|      si1|  Robin|     M|1981-12-13 00:00:00|      si1|   Java|   81|
|      si1|  Robin|     M|1981-12-13 00:00:00|      si1| Python|   75|
|      si2|  Maria|     F|1986-06-06 00:00:00|      si2|   Java|   83|
|      si2|  Maria|     F|1986-06-06 00:00:00|      si2| Python|   85|
|      si3|  Julie|     F|1988-09-05 00:00:00|      si3|   Ruby|   72|
|      si3|  Julie|     F|1988-09-05 00:00:00|      si3|   Java|   76|
|      si4|    Bob|     M|1987-05-04 00:00:00|      si4| Python|   84|
|      si4|    Bob|     M|1987-05-04 00:00:00|      si4|    C++|   78|
|      si6|William|     M|1980-11-12 00:00:00|     null|   null| null|
+---------+-------+------+-------------------+---------+-------+-----+
```

At the very end of the output, note that William’s row is available
but the subject is null. This will be very useful and needed for showing
scenarios when a student is absent and has not taken the test.
There often are columns that are not required for output. So, let’s
show only the columns that are needed. As an example, we are going to
select the `studentId`, `name`, `subjects`, and `marks` columns only
for reporting purposes.

Running this query:

```
>>> spark.sql("select studentid,name,subject,marks from studentsdob st left join subjects sb on (st.studentId = sb.studentId)").show()
```

We get this output with an error:

```
pyspark.sql.utils.AnalysisException: "Reference 'studentid' is ambiguous, could be: st.studentid, sb.studentid.
```

As you will be aware by now, this happens because `studentId` is not
qualified with an alias name. So let’s qualify the columns with alias
names:

```
>>> spark.sql("select st.studentid,name,subject,marks from studentsdob st left join subjects sb on (st.studentId = sb.studentId)").show()
```

Here is the output with reduced columns:

```
+---------+-------+-------+-----+
|studentid|   name|subject|marks|
+---------+-------+-------+-----+
|      si1|  Robin|   Java|   81|
|      si1|  Robin| Python|   75|
|      si2|  Maria|   Java|   83|
|      si2|  Maria| Python|   85|
|      si3|  Julie|   Ruby|   72|
|      si3|  Julie|   Java|   76|
|      si4|    Bob| Python|   84|
|      si4|    Bob|    C++|   78|
|      si6|William|   null| null|
+---------+-------+-------+-----+
```

Observation 2
Now let’s look into the second observation, where there is a record
in the `subjects`
dataset that did not appear in the output. This could be due to multiple
reasons, such as the student’s data was not properly maintained, data
entry errors occurred while entering `studentId` values,
etc. All of these kinds of errors are very common in Big Data volumes.
These errors cannot be ignored since they will introduce data errors and
incorrect data reporting. For these reasons, let’s try to include that
value in the report using a right outer join.  

The following query uses a right outer join:

```
>>> spark.sql("select st.studentid,name,sb.studentid,subject,marks from studentsdob st right join subjects sb on (st.studentId = sb.studentId)").show()
```

Note that the only difference between the left and right outer joins
is replacing the word “left” with “right”.

Here is the output: 

```
+---------+-----+---------+-------+-----+
|studentid| name|studentid|subject|marks|
+---------+-----+---------+-------+-----+
|      si1|Robin|      si1| Python|   75|
|      si3|Julie|      si3|   Java|   76|
|      si1|Robin|      si1|   Java|   81|
|      si2|Maria|      si2| Python|   85|
|      si3|Julie|      si3|   Ruby|   72|
|      si4|  Bob|      si4|    C++|   78|
|     null| null|      si5|      C|   77|
|      si4|  Bob|      si4| Python|   84|
|      si2|Maria|      si2|   Java|   83|
+---------+-----+---------+-------+-----+
```

Note that subject C is available in the output. But the `name` and `studentid` fields are
null. That is because `studentId si5` is not
available in the `students` dataset.

Now we can show the missing subjects using a left outer join or a
right outer join. But having only one subject in the output will not be
useful. We need to be able to list both of the missing entities in one
output. In such scenarios, we need to use a full outer join, which will
match based on the `join` condition and
will select rows from both the tables.  

Let’s apply the same query with a full outer join.

```
>>> spark.sql("select st.studentid,name,sb.studentid,subject,marks from studentsdob st FULL OUTER JOIN subjects sb on (st.studentId = sb.studentId)").show()
```

Note that a full outer join is a very costly operation. Applying a
full outer join on Big Data volumes will consume lots of resources and
time. So it is better to avoid full outer joins in time-critical
SLA-based applications.

Here is the output: 

```
+---------+-------+---------+-------+-----+
|studentid|   name|studentid|subject|marks|
+---------+-------+---------+-------+-----+
|     null|   null|      si5|      C|   77|
|      si2|  Maria|      si2| Python|   85|
|      si2|  Maria|      si2|   Java|   83|
|      si4|    Bob|      si4|    C++|   78|
|      si4|    Bob|      si4| Python|   84|
|      si3|  Julie|      si3|   Java|   76|
|      si3|  Julie|      si3|   Ruby|   72|
|      si6|William|     null|   null| null|
|      si1|  Robin|      si1| Python|   75|
|      si1|  Robin|      si1|   Java|   81|
+---------+-------+---------+-------+-----+
```

Note that William and subject C are both available. Now that we are
able to see these inconsistencies, we can work with the data sources to
fix them.
While applying the join, we need to identify the columns with which
we can apply it. It is always better if we can have numerical columns
since the comparison will be faster and less error prone to any data
issues. Usually when you are working with Big Data volumes, you’ll find
string columns that are not very clean. For example, there may be
trailing or leading spaces, case-sensitivity issues, and so on, that
will make these joins fail. So you need to first cleanse these data
differences and then apply the join.
With this we have successfully completed the assignment of joining two DataFrames.

Recipe 6-7\. Join Multiple DataFrames Using SQL

Problem
You want to join more than two DataFrames and see the output.

Solution

For simplicity reasons, we use a simple dataset related to students. So
far we have looked at students and subjects. Now we add a new dataset,
called `attendance`. Let’s
look at all three datasets together, shown in Figure [6-4](#469054_1_En_6_Chapter.xhtml#Fig4).

![../images/469054_1_En_6_Chapter/469054_1_En_6_Fig4_HTML.jpg](images/469054_1_En_6_Chapter/469054_1_En_6_Fig4_HTML.jpg)

Figure 6-4
The new attendance dataset has been added

This dataset has an `attendance` column,
which registers how many days out of 40 classes each student attended
for each subject.
Now that you are aware of how to handle errors in data using left
joins, right joins, and full outer joins, I have cleansed the data and
simplified it.

Figures [6-5](#469054_1_En_6_Chapter.xhtml#Fig5) and [6-6](#469054_1_En_6_Chapter.xhtml#Fig6) show the
cleansed students and the subjects data.

![../images/469054_1_En_6_Chapter/469054_1_En_6_Fig6_HTML.jpg](images/469054_1_En_6_Chapter/469054_1_En_6_Fig6_HTML.jpg)

Figure 6-6
Students data

![../images/469054_1_En_6_Chapter/469054_1_En_6_Fig5_HTML.jpg](images/469054_1_En_6_Chapter/469054_1_En_6_Fig5_HTML.jpg)

Figure 6-5
Subjects data

How It Works
To be able to start applying queries, we
first need to load the attendance data. Let’s go ahead and load the
`attendance`
dataset.

Here are the steps to load the `attendance` dataset:

```
>>> attendanceDf = spark.read.csv('attendance.csv',header=True, inferSchema=True)
>>> attendanceDf.createOrReplaceTempView("attendance")
>>> spark.sql("select * from attendance").show()
```

Here is the output:

```
+---------+-------+----------+
|studentId|subject|attendance|
+---------+-------+----------+
|      si1| Python|        30|
|      si3|   Java|        22|
|      si1|   Java|        34|
|      si2| Python|        39|
|      si3|   Ruby|        25|
|      si4|    C++|        38|
|      si5|      C|        35|
|      si4| Python|        39|
|      si2|   Java|        39|
|      si6|   Java|        35|
+---------+-------+----------+
```

Before going to the final report, let’s first combine all three tables
to verify the joins. Here’s the query to join multiple dataframes:

```
>>> spark.sql("select * from studentsdob st Join subjects sb on (st.studentId = sb.studentId) Join attendance at on (at.studentId = st.studentId)").show()
```

This query is joining the third dataset as a continuation of the
existing join. Similarly, you can join any number of tables in the same
manner.

Here is the output:

```
+---------+-------+------+-------------------+---------+-------+-----+---------+-------+----------+
|studentId|   name|gender|        dateofbirth|studentId|subject|marks|studentId|subject|attendance|
+---------+-------+------+-------------------+---------+-------+-----+---------+-------+----------+
|      si1|  Robin|     M|1981-12-13 00:00:00|      si1|   Java|   81|      si1|   Java|        34|
|      si1|  Robin|     M|1981-12-13 00:00:00|      si1|   Java|   81|      si1| Python|        30|
|      si1|  Robin|     M|1981-12-13 00:00:00|      si1| Python|   75|      si1|   Java|        34|
|      si1|  Robin|     M|1981-12-13 00:00:00|      si1| Python|   75|      si1| Python|        30|
|      si2|  Maria|     F|1986-06-06 00:00:00|      si2|   Java|   83|      si2|   Java|        39|
|      si2|  Maria|     F|1986-06-06 00:00:00|      si2|   Java|   83|      si2| Python|        39|
|      si2|  Maria|     F|1986-06-06 00:00:00|      si2| Python|   85|      si2|   Java|        39|
|      si2|  Maria|     F|1986-06-06 00:00:00|      si2| Python|   85|      si2| Python|        39|
|      si3|  Julie|     F|1988-09-05 00:00:00|      si3|   Ruby|   72|      si3|   Ruby|        25|
|      si3|  Julie|     F|1988-09-05 00:00:00|      si3|   Ruby|   72|      si3|   Java|        22|
|      si3|  Julie|     F|1988-09-05 00:00:00|      si3|   Java|   76|      si3|   Ruby|        25|
|      si3|  Julie|     F|1988-09-05 00:00:00|      si3|   Java|   76|      si3|   Java|        22|
|      si4|    Bob|     M|1987-05-04 00:00:00|      si4| Python|   84|      si4| Python|        39|
|      si4|    Bob|     M|1987-05-04 00:00:00|      si4| Python|   84|      si4|    C++|        38|
|      si4|    Bob|     M|1987-05-04 00:00:00|      si4|    C++|   78|      si4| Python|        39|
|      si4|    Bob|     M|1987-05-04 00:00:00|      si4|    C++|   78|      si4|    C++|        38|
|      si6|William|     M|1980-11-12 00:00:00|      si6|   Java|   70|      si6|   Java|        35|
+---------+-------+------+-------------------+---------+-------+-----+---------+-------+----------+
```

It’s very hard to read from this output since it contains all the
columns from all three tables.
Now the task is to get a cleaner report with only the required
columns by combining all the datasets—`student`, `subject`, `attendance`—that will
provide a report:
**Name    Gender    Subject    Marks    Attendance**

Here is all the code that is needed to build the report:

```
#Load and register Student data
studentDf = spark.read.csv('studentData_dateofbirth.csv', header=True, inferSchema=True)
studentDf.createOrReplaceTempView("StudentsDob")
#Load and register subjects
subjectsDf = spark.read.csv('subjects.csv',header=True, inferSchema=True)
subjectsDf.createOrReplaceTempView("subjects")
#Load and register attendance
attendanceDf = spark.read.csv('attendance.csv',header=True, inferSchema=True)
attendanceDf.createOrReplaceTempView("attendance")
#Create the gender User Defined Function
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType
def genderCodeToValue(code):
return('FEMALE' if code == 'F' else 'MALE' if code == 'M' else 'NA')
spark.udf.register("genderCodeToValue", genderCodeToValue, StringType())
# Apply a query to get the final report
spark.sql("select name as Name,  genderCodeToValue(gender) as Gender, marks as Marks, attendance Attendance  from studentsdob st Join subjects sb on (st.studentId = sb.studentId) Join attendance at on (at.studentId = st.studentId)").show()
```

The output of this code is as follows:

```
+-------+------+-----+----------+
|   Name|Gender|Marks|Attendance|
+-------+------+-----+----------+
|  Robin|  MALE|   81|        34|
|  Robin|  MALE|   81|        30|
|  Robin|  MALE|   75|        34|
|  Robin|  MALE|   75|        30|
|  Maria|FEMALE|   83|        39|
|  Maria|FEMALE|   83|        39|
|  Maria|FEMALE|   85|        39|
|  Maria|FEMALE|   85|        39|
|  Julie|FEMALE|   72|        25|
|  Julie|FEMALE|   72|        22|
|  Julie|FEMALE|   76|        25|
|  Julie|FEMALE|   76|        22|
|    Bob|  MALE|   84|        39|
|    Bob|  MALE|   84|        38|
|    Bob|  MALE|   78|        39|
|    Bob|  MALE|   78|        38|
|William|  MALE|   70|        35|
+-------+------+-----+----------+
```

7. Optimizing PySpark SQL

In this chapter, we look at various Spark SQL recipes that optimize
SQL queries. Apache Spark is an open source framework that is developed
with Big Data volumes in mind. It is supposed to handle huge volumes of
data. It is supposed to be used in scenarios where there is a need for
horizontal scaling
for processing power. Before we cover the optimization techniques used
in Apache Spark, you need to understand the basics of horizontal scaling
and vertical scaling.

The term *scaling* refers to when there is a need to increase the
compute power or the memory capacity of the system. Vertical scaling can be
defined as adding more processing capacity or power to an existing
machine. For example, when we have a machine that has 8GB RAM and are
processing 2GB of data. If there is an increase in the data volume from
2GB to 4GB, we might want to extend the RAM from 8GB to 16GB. For this,
we add an extra capacity of 8GB RAM. This will make the machine have
16GB RAM in total. This mechanism of improving the performance by
extending the resources of the existing machine is called  *vertical scaling*
 (see Figure [7-1](#469054_1_En_7_Chapter.xhtml#Fig1))*.*

![../images/469054_1_En_7_Chapter/469054_1_En_7_Fig1_HTML.png](images/469054_1_En_7_Chapter/469054_1_En_7_Fig1_HTML.png)

Figure 7-1
Vertical
scaling

On the other hand, in horizontal scaling, we add a machine to the
existing machine and make it two machines to improve the capacity.
Taking the same example, we add another machine with a capacity of 8GB
and make two machines processing the 4GB data, which is called
*horizontal scaling* (see Figure [7-2](#469054_1_En_7_Chapter.xhtml#Fig2)).

![../images/469054_1_En_7_Chapter/469054_1_En_7_Fig2_HTML.png](images/469054_1_En_7_Chapter/469054_1_En_7_Fig2_HTML.png)

Figure 7-2
Horizontal
scaling

Since this is a very important concept to understand, let’s look at a
simple example. Let’s assume we are travelling from place A to place B
and we need to accommodate 20 people with a bus that has 20 passenger
seats. All is good since we have the seat capacity to satisfy the number
of passengers. Let’s say we now have to accommodate 30 passengers. We
can get a bigger bus that has a seating capacity of 30, such as a double
decker bus. In this scenario, we are going to keep only one bus, but it
has increased capacity. So essentially we have scaled from a
20-passenger capacity bus to 30-passenger capacity bus, and we still use
just one bus. This is vertical scaling, where we are able to scale up
using only one bus.
The issue with vertical
scaling is that there is a limit to the level of scaling we can
do. For example, if we get 60 or 70 passengers, we may not be able to
find just one bus able to accommodate them all. So there is a limit to
how much we can scale up vertically. Also if that one bus is faced with
any maintenance issues then we will not be able to travel at all. This
example is similar to software vertical scaling, where there is also a
limit to how much we can scale up vertically. It also has the problem of
a single point of failure. If one big fat machine fails, our entire
system will go down.
Horizontal
scaling addresses most of these problems. Let’s look at
horizontal scaling more. Let’s say we add a bus with a capacity of 20
and are now using two buses. So essentially we have scaled out by adding
one more bus to our existing fleet. This kind of scaling is called
horizontal scaling. While at the outset it seems to address many of the
issues of vertical scaling, it comes with its own set of issues. For
example, in the same bus example, we will need to make sure both buses
are in sync. We will have to take extra steps in making sure all the
passengers are onboard by checking in with both the buses. We might need
to always synchronize the plans for both of the buses. Also we will need
to have more drivers whom we need to manage. These are some of the extra
steps that we need to take while adding another bus.
Very similar to this, with horizontal scaling, we need to make sure
the distributed processes are synchronized. We will need to make sure
the output from all the machines is consolidated into one final
output.
Now that we have covered vertical scaling and horizontal scaling, let’s
look at scaling from a perspective of Big Data. Since Big Data is
supposed to handle huge volumes of data, vertical scaling is not really
an option. We have to go with horizontal scaling. Since there is going
to terabytes and petabytes of data that needs to be processed, we will
need to keep adding more machines to do the processing. We also need
software that works in a distributed environment. Regular windows
filesystems, such as FAT and NTFS, and UNIX filesystems such as EXT
cannot work in a distributed set of machines.
HDFS is one such filesystem that can operate on distributed sets of
machines. HDFS can be started with a five machine/node setup. With more
and more data, it can be easily expanded to a distributed multi-node
system. Other than HDFS, Amazon S3 is another example of a distributed
filesystem. While such distributed filesystems know how to store the
data in a distributed manner, we still need to apply the compute in a
distributed manner.

For example, let’s assume we need to find the average of all students
available in a file that is distributed across two machines. The compute
process should find the average from System-1 and from System-2 and
finally it should know how to find the average from these averages as
well. This makes the compute process more complicated in a distributed
setup. See Figure [7-3](#469054_1_En_7_Chapter.xhtml#Fig3).

![../images/469054_1_En_7_Chapter/469054_1_En_7_Fig3_HTML.png](images/469054_1_En_7_Chapter/469054_1_En_7_Fig3_HTML.png)

Figure 7-3
A distributed setup

Even for simple operations, you need sophisticated software that can
handle the distributed nature of compute. Hadoop includes both an HDFS
and a MapReduce framework that can handle distributed computations from
multiple nodes. The MapReduce framework contains a Map phase that tries
to apply the compute on all the machines and get the intermediate
output. The Reduce phase works on combining the intermediate output into
one single output. In the previous example if the average is to be found
using the MapReduce framework, the Map phase can find the average of
students from each of the files, while the Reduce phase finds the final
output which computes an average of averages. Figure [7-4](#469054_1_En_7_Chapter.xhtml#Fig4) shows the MapReduce framework at a
high level.

![../images/469054_1_En_7_Chapter/469054_1_En_7_Fig4_HTML.png](images/469054_1_En_7_Chapter/469054_1_En_7_Fig4_HTML.png)

Figure 7-4
Average using the MapReduce framework and assuming the same number of
students in each node

While Hadoop’s MapReduce framework is a
good fit for distributed compute, it has lots of boilerplate code that
needs to be written. It also involves a lot of disk I/O, which
drastically impacts performance. Users need to understand the complex
framework and it is very difficult for many users. That is how Apache
Spark saves the day.
Apache Spark
can easily handle such distributed compute. It provides an easy-to-use
interface for users. It removes most of the boilerplate code required by
MapReduce and reduces the 50 lines of code in the MapReduce framework to
5-10 lines of code.
Apache Spark also introduced in-memory processing, which tends to
drastically speed up the slowly performing disk I/O. Operations that are
very repetitive in nature can cache the data in memory and can be used
multiple times. All of those together make Apache Spark the one-stop
shop for Big Data processing.
Even though Apache Spark natively supports processing huge volumes of
data in a distributed manner, we need to provide spark with the right
environment in terms of properly distributing the data. For example, if
in a 10-node cluster all the data is located in just three nodes and the
other seven nodes are with empty data or very little data, as you can
see, only the three nodes are going to do all the work. The remaining
nodes will be silent. This will result in performance reduction. You
need to supply Spark programs with the right distribution of data or you
need to provide the right tuning parameters that will accordingly
distribute the data and improve the performance.
Similarly, if there are joins on huge tables with a join key that’s
skewed or not distributed properly, there will be a lot of shuffle. In
such scenarios, Spark will get into out of memory situations. We then
need to use the right performance improvement parameters.
These are just some of the scenarios in which Spark needs you to
optimize the queries so that you get the best performance from the Spark
SQL.
Now that we have covered the context around performance optimization,
let’s go through the recipes of this chapter step-by-step and learn the
PySpark optimization techniques.

We will go through the following recipes:

*   Recipe 7-1\. Apply aggregation using PySpark SQL

*   Recipe 7-2\. Apply Windows functions using PySpark SQL

*   Recipe 7-3\. Cache data using PySpark SQL

*   Recipe 7-4\. Apply the Distribute By, Sort By, and Cluster By
    clauses in PySpark SQL

Let’s get into the first recipe.

Recipe 7-1\. Apply Aggregation Using PySpark SQL

Problem
You want to use Group By in PYSpark SQL to find the average marks per
student. So far you have created the students report and shown their
respective subjects and marks from the `students` and `subjects` data. Let’s
try to get a report on students to identify the average marks for each
of them.

Solution

We load the `Students` and `Subjects` datasets and
join them together:

```
>>> #Load and register Student data
>>>studentDf = spark.read.csv('studentData_dateofbirth.csv',header=True, inferSchema=True)
>>> studentDf.createOrReplaceTempView("StudentsDob")
>>> #Load and register subjects
>>>subjectsDf = spark.read.csv('subjects.csv',header=True, inferSchema=True)
>>> subjectsDf.createOrReplaceTempView("subjects")
>>> spark.sql("select * from studentsdob st join subjects sb on (st.studentId = sb.studentId)").show()
```

Here is the output:

```
+---------+-------+------+-------------------+---------+-------+-----+
|studentId|   name|gender|        dateofbirth|studentId|subject|marks|
+---------+-------+------+-------------------+---------+-------+-----+
|      si1|  Robin|     M|1981-12-13 00:00:00|      si1|   Java|   81|
|      si1|  Robin|     M|1981-12-13 00:00:00|      si1| Python|   75|
|      si2|  Maria|     F|1986-06-06 00:00:00|      si2|   Java|   83|
|      si2|  Maria|     F|1986-06-06 00:00:00|      si2| Python|   85|
|      si3|  Julie|     F|1988-09-05 00:00:00|      si3|   Ruby|   72|
|      si3|  Julie|     F|1988-09-05 00:00:00|      si3|   Java|   76|
|      si4|    Bob|     M|1987-05-04 00:00:00|      si4| Python|   84|
|      si4|    Bob|     M|1987-05-04 00:00:00|      si4|    C++|   78|
|      si6|William|     M|1980-11-12 00:00:00|      si6|   Java|   70|
+---------+-------+------+-------------------+---------+-------+-----+
```

You might remember this output from the previous chapter. Let’s try
to use Group By on this query to identify the average marks per
student.

How It Works

Similar to the issue with UDFs, if we use the `avg` function as
follows, it will not work.

```
>>> spark.sql("select name, avg(marks) from studentsdob st join subjects sb on (st.studentId = sb.studentId)").show()
```

Here is the error that we will get:

```
py4j.protocol.Py4JJavaError: An error occurred while calling o24.sql.
: org.apache.spark.sql.AnalysisException: grouping expressions sequence is empty, and 'st.`name`' is not an aggregate function. Wrap '(avg(CAST(sb.`marks` AS BIGINT)) AS `avg(marks)`)' in windowing function(s) or wrap 'st.`name`' in first() (or first_value) if you don't care which value you get.;
```

Note that we haven’t used the Group By clause in this
query. If we use an aggregate function without applying a Group By
clause, we will get an error. While the UDF operates on one row per
call, `avg` is
actually what Spark calls *User Defined Aggregate Functions*
(UDAF). These
kinds of functions take a set of rows based on the Group By clause and
apply the functionality on those sets of rows. Let’s see the Group By in
action.

Here is the query that uses Group By:

```
>>> spark.sql("select name,avg(marks) from studentsdob st left join subjects sb on (st.studentId = sb.studentId) group by name").show()
```

Note from this query that `group by name` applies
Group By on the `name` column. The
`select` clause
only selects `name` and `avg(marks)`. If you
want to determine how to select the columns on which you want to apply
the group column, remember that the columns that come in the `Per` section of the
report (for example per student or per subject) will be the columns that
participate in the Group By clause. You can also have multiple columns
or expressions in the Group By clause. For example, if we want to find
avg marks per student on a subject, we need to include both the name and
the subject column in the Group By clause.

Here is the output of the query:

```
+-------+----------+
|   name|avg(marks)|
+-------+----------+
|  Robin|      78.0|
|    Bob|      81.0|
|  Julie|      74.0|
|  Maria|      84.0|
|William|      70.0|
+-------+----------+
```

The following query uses two columns in the Group By clause:

```
>>> spark.sql("select name,subject,avg(marks) from studentsdob st left join subjects sb on (st.studentId = sb.studentId) group by name,subject").show()
```

We are using both `name` and `subject` in the Group
By. This will bring all the records that are unique for students and
subjects. If the data contains marks obtained by students per subjects
for all the semesters, this will provide us with the overall average
marks of the students per subject.

Here is the output:

```
+-------+-------+----------+
|   name|subject|avg(marks)|
+-------+-------+----------+
|William|   Java|      70.0|
|    Bob| Python|      84.0|
|  Robin| Python|      75.0|
|  Robin|   Java|      81.0|
|  Maria| Python|      85.0|
|  Maria|   Java|      83.0|
|    Bob|    C++|      78.0|
|  Julie|   Ruby|      72.0|
|  Julie|   Java|      76.0|
+-------+-------+----------+
```

Step 7-1-1\. Finding the Number of Students
per Subject
In this step, we are going to find the number of students who have taken
each test. Let’s write the query to get that report.

Here is the query:

```
>>> spark.sql("select subject,count(name) Students_count from studentsdob st left join subjects sb on (st.studentId = sb.studentId) group by subject").show()
```

Here is the output:

```
+-------+--------------+
|subject|Students_count|
+-------+--------------+
|    C++|             1|
|   Ruby|             1|
| Python|             3|
|   Java|             4|
+-------+--------------+
```

Step 7-1-2\. Finding the Number of Subjects
per Student
Similarly, we are going to determine how many subjects on which each
student has taken a test.

Here is the query:

```
>>> spark.sql("select name,count(subject) Students_count from studentsdob st left join subjects sb on (st.studentId = sb.studentId) group by name").show()
```

Here is the output:

```
+-------+--------------+
|   name|Students_count|
+-------+--------------+
|  Robin|             2|
|    Bob|             2|
|  Julie|             2|
|  Maria|             2|
|William|             1|
+-------+--------------+
```

These queries show the process of using Group By clauses to apply
aggregate functions. These aggregate functions come in handy when we are
preparing data for any report. In everyday business settings, these are
reports you will be repeatedly using. Especially when it comes to data
analytics, where you are going to get insights from data, you will need
to slice and dice the data into various aspects to get meaningful
information from it. So it is very important to be able to appreciate
the use of the Group By clause in Spark SQLs.
Other UDAFs (User Defined Aggregate Functions) available in PySpark
SQL are `sum()`,`count()`, `countDistinct()`,
`max()`, `min()`, etc.
It is important to note that keeping these UDFs and UDAFs lean is
very important from a performance optimization perspective. Since these
UDFs and UDAFs would be run in JVMs, the data needs to be serialized
before passing it to the JVM. So there could be significant performance
impact when these functions are incorrectly used. Similarly, since these
functions are applied on millions of rows in the Big Data space,
avoiding log and print statements are good practices. For example, a UDF
that takes just a few seconds could pose serious performance issues when
it’s run on a data volume of 10 billion rows.

Recipe 7-2\. Apply Windows Functions Using PySpark
SQL

Problem
You want to find the student scoring first and second in each of the
subjects.

Solution
Apache Spark supports a special type of function, called
*windows* functions, along with its built-in UDFs such as `substr`, `round`, etc. and
aggregate functions such as `sum` and `avg`. While these UDFs
and aggregate functions can perform most operations, there still are
many operations that cannot be performed by these functions. So Spark
introduces windows functions, which can operate on a set of rows while
operating on a single row.

How It Works

Step 7-2-1\. Applying the Rank Function to Get the
Rank
This step uses the `rank` windows function. The `rank` function
provides a sequential number for each row within a selected set of rows.
Initially I am going to apply the `rank` function on all
the rows without any window selection. Let’s see how to do that.

Here is the query that uses `rank` in its simplest
form:

```
>>> spark.sql("select name,subject,marks,rank() over (order by name) as RANK from studentsdob st join subjects sb ON (st.studentId = sb.studentId)").show()
```

From this query you can see the `rank()` function  being used like any
other user-defined function. Note that there is a `over` clause
immediately after the `rank()` function. This
`over` clause
defines the window of rows for the `rank` function. This
window needs to identify the set of rows and the ordering of those
rows.
In this query, we are not using a set of rows. We are using all the
rows in the table as one set. Next we use the `ORDER BY name`*.* This is used to order the rows using the `name` column. Based on
the order you are going to see, a rank is given to each of the rows. The
query in this form may not be very helpful. But it’s here for simplicity
reasons and for you to understand. Let’s look at it step by step.

Here is the output:

```
+-------+-------+-----+----+
|   name|subject|marks|RANK|
+-------+-------+-----+----+
|    Bob|    C++|   78|   1|
|    Bob| Python|   84|   1|
|  Julie|   Java|   76|   3|
|  Julie|   Ruby|   72|   3|
|  Maria| Python|   85|   5|
|  Maria|   Java|   83|   5|
|  Robin| Python|   75|   7|
|  Robin|   Java|   81|   7|
|William|   Java|   70|   9|
+-------+-------+-----+----+
```

Step 7-2-2\. Using Partition By in a Rank
Function

In the previous step we learned to apply the `rank` function . Now we will learn to
use the `PARTITION` clause to
classify the rows as multiple sets. You can visualize this as keeping a
window over those set of rows, as shown in Figure [7-5](#469054_1_En_7_Chapter.xhtml#Fig5).

![../images/469054_1_En_7_Chapter/469054_1_En_7_Fig5_HTML.jpg](images/469054_1_En_7_Chapter/469054_1_En_7_Fig5_HTML.jpg)

Figure 7-5
Classifying rows as multiple sets

As shown in Figure [7-5](#469054_1_En_7_Chapter.xhtml#Fig5), there are four
windows based on the subject. Each windows has the same subject. The
C++, Java, Python, and Ruby subjects are created. Now let’s assume for
each row, there are specific actions that need to be performed based on
the rows. Then we can apply the window functions. Examples of such
functions are:

*   What is the average age of the students in their participating
    subjects?

*   What is the percentile of marks obtained by the students in their
    participating subjects?

It is very hard to write such functions in pure SQL without window
functions. Let’s go ahead and create the windows using the `PARTITION` clause .

```
>>> spark.sql("select name,subject,marks,rank() over (PARTITION BY subject order by name) as RANK from studentsdob st join subjects sb ON (st.studentId = sb.studentId)").show()
```

In this query we added a new `PARTITION` clause on
the subject. This is to say that we are windowing based on the subject
column. This will create a separate window for each distinct subject
column value and will apply the window function rank only within the
rows of the window set.

Here is the output:

```
From this output you can see that rank has been assigned to each of the subjects to students.
+-------+-------+-----+----+
|   name|subject|marks|RANK|
+-------+-------+-----+----+
|    Bob|    C++|   78|   1|
|  Julie|   Ruby|   72|   1|
|    Bob| Python|   84|   1|
|  Maria| Python|   85|   2|
|  Robin| Python|   75|   3|
|  Julie|   Java|   76|   1|
|  Maria|   Java|   83|   2|
|  Robin|   Java|   81|   3|
|William|   Java|   70|   4|
+-------+-------+-----+----+
```

But wait, note that we are not getting the correct rank. This is
because the Order By column uses `name` rather than
`marks`. Let go
ahead and correct this to use the `marks` column for
ordering the students in each of the subjects.

Here is the query:

```
>>> spark.sql("select name,subject,marks,rank() over (PARTITION BY subject order by marks desc) as RANK from studentsdob st join subjects sb ON (st.studentId = sb.studentId)").show()
```

Note that I have changed the ordering based on `marks`. Also I am
making sure the data is in ascending order. This is to make sure the
students with the highest mark get the first rank.

```
+-------+-------+-----+----+
|   name|subject|marks|RANK|
+-------+-------+-----+----+
|    Bob|    C++|   78|   1|
|  Julie|   Ruby|   72|   1|
|  Maria| Python|   85|   1|
|    Bob| Python|   84|   2|
|  Robin| Python|   75|   3|
|  Maria|   Java|   83|   1|
|  Robin|   Java|   81|   2|
|  Julie|   Java|   76|   3|
|William|   Java|   70|   4|
+-------+-------+-----+----+
```

Step 7-2-3\. Obtaining the Top Two Students for Each
Subject
Now that we have the ranking in place, let’s
identify the first two students only in the report. This is very easy.
We need to filter on the `rank` column for
values less than or equal to two.

Here is the query:

```
>>> spark.sql("select name,subject,marks,RANK from (select name,subject,marks,rank() over (PARTITION BY subject order by marks desc) as RANK from studentsdob st join subjects sb ON (st.studentId = sb.studentId)) where RANK <= 2").show()
```

Here is the output. As we can see from this query, a subquery alias
uses the `RANK`
column in the filter. This is because, as we saw in the previous
chapter, an alias column created in a query cannot be used in the same
query. So we need to make it a subquery and apply the filter from the
parent query.

Here is the output:

```
+-----+-------+-----+----+
| name|subject|marks|RANK|
+-----+-------+-----+----+
|  Bob|    C++|   78|   1|
|Julie|   Ruby|   72|   1|
|Maria| Python|   85|   1|
|  Bob| Python|   84|   2|
|Maria|   Java|   83|   1|
|Robin|   Java|   81|   2|
+-----+-------+-----+----+
```

We have successfully used the windows `rank()` function and
covered the concept of windowing functions. Using this concept, we can
use any windows function.
Note that when we are applying these window functions on huge volumes
of data in a Big Data world, it’s going to add lots of performance
constraints. Especially when we are joining big tables, there will be a
drastic performance degradation. So it is always a good practice to use
these windows functions as pre-computed values and then use those values
in the joins. We have to make sure we are using the `PARTITION` clause in
the right manner. We are going to cover how to use `PARTITION` in the next
recipe.

Recipe 7-3\. Cache Data Using PySpark SQL

Problem
You want to cache a table or dataset.

Solution
Many times when there is a dataset that we have to
use repeatedly, we don’t want to read it every time we use it. For
example, data containing country and country code will be smaller in
size. This data could fit in the memory very easily without occupying a
lot of space. But we wouldn’t want such data to be read each time it is
used. So for such scenarios, we can use the cache provided by Spark
using PySpark SQL.
When it comes to Machine Learning algorithms that involve applying
logic over the same dataset multiple times, caching is the best option
to get the best performance. With caching Spark will keep the data in
memory so it needs to read it again. Reading the data from disk is a
costly operation. For all its advantages, let’s try to use caching in
the example.

How It Works
In this example, we are going to first cache the students data and
create a cached view. After that, we apply a join that cached data with
the subject and assignments.

Here is the query to cache the data. Here we will provide a name to the
cached data very similar to a view or a table.

```
>>> spark.sql("cache table students_cached AS select * from studentsdob").show()
```

Let’s try to use the cached data in queries.

Here is the query that uses the newly created cached table called `students_cached`:

```
>>> spark.sql("select st.studentid,name,subject,marks from students_cached st left join subjects sb on (st.studentId = sb.studentId)").show()
```

As you can see from this query, there is no difference in using a cached
table from using a regular table, except that we have obtained a lot of
performance benefits.

```
+---------+-------+-------+-----+
|studentid|   name|subject|marks|
+---------+-------+-------+-----+
|      si1|  Robin|   Java|   81|
|      si1|  Robin| Python|   75|
|      si2|  Maria|   Java|   83|
|      si2|  Maria| Python|   85|
|      si3|  Julie|   Ruby|   72|
|      si3|  Julie|   Java|   76|
|      si4|    Bob| Python|   84|
|      si4|    Bob|    C++|   78|
|      si6|William|   Java|   70|
+---------+-------+-------+-----+
```

While caching is very helpful, we need to use caching very carefully.
We don’t want to cache huge volumes of data. It will lead to out of
memory exceptions. Handling huge volumes of data in memory will leave
Spark with less memory for other operations. We could get an out of
memory error while performing a totally different operation, and it
would actually be due to caching huge volumes data in memory.

Recipe 7-4\. Apply the Distribute By, Sort By, and
Cluster By Clauses in PySpark SQL

Problem
You want to use Distribute By in PySpark SQL to effectively partition
your data.

Solution
As explained at the start of this chapter , the key to getting
highly effective data transformation with respect to high performance
and optimum use of resources in a distributed computing environment
solely depends on how effectively the data is distributed. If the data
is equally distributed among all the data nodes, the Spark job will
perform in an equally distributed manner. If not, we end up with some of
the tasks taking the maximum time and resources while others finishing
in a few seconds.

There are many occasions when you will provide maximum resources to your
job in terms of the number of executors and executor memory. But you
will still end up waiting for the job to complete with just a couple of
executors running for hours while the rest have long finished. Let’s
look at the reasons and see how to avoid such behaviors. There are
multiple reasons why jobs behave this way:

*   Most of the data is placed in one of the nodes, due to which the
    default partition provided by Spark is not effective.

*   The key based on which you are joining the data is skewed because
    the join operation is happening in a few allocated executors.

In both of these scenarios, the data is not partitioned correctly.
Spark provides mechanisms for users to determine how to repartition data
from Spark’s original partition. While it is called *repartition*
in the Spark programmatic API, it is called *distribute* By
PySpark SQL. This technique is one of the best ways to optimize Spark
SQLs, yet it is not widely used because not many are aware of these
clauses.
Let’s see how to use Distribute By in PySpark SQL

How It Works
This example applies the Distribute By clause on
`studentId` since
it’s the join key.
In the following queries, we create two new DataFrames called `student_distributed`
and `subjects_distributed`.
If you look at the query, we are applying the Distribute By clause on
the `studentId`
column .
After creating those DataFrames, we also register it as a temporary
view. Up until now, there has been no action performed by Spark. When
there is a call for an action further down, Spark will repartition the
data based on `studentId`, so that
the data will be evenly distributed among all the compute nodes.

Here are the queries:

```
>>> student_distributed = spark.sql("select * from StudentsDob distribute by studentId")
>>> subjects_distributed = spark.sql("select * from subjects distribute by studentId")
>>> student_distributed.createOrReplaceTempView("student_distributed")
>>> subjects_distributed.createOrReplaceTempView("subjects_distributed")
```

There will not be any output from these queries since we are not
calling the `show()` method and
there are no actions being performed with this.
Once we have distributed views, we can start using these views in the
query. Let’s join the `students` and `subjects` datasets
based on the newly created distributed views .

Here is the query:

```
>>> spark.sql("select st.studentid,name,subject,marks from student_distributed st left join subjects_distributed sb on (st.studentId = sb.studentId)").show()
```

Here is the output:

```
+---------+-------+-------+-----+
|studentid|   name|subject|marks|
+---------+-------+-------+-----+
|      si2|  Maria|   Java|   83|
|      si2|  Maria| Python|   85|
|      si4|    Bob| Python|   84|
|      si4|    Bob|    C++|   78|
|      si3|  Julie|   Ruby|   72|
|      si3|  Julie|   Java|   76|
|      si6|William|   Java|   70|
|      si1|  Robin|   Java|   81|
|      si1|  Robin| Python|   75|
+---------+-------+-------+-----+
```

Understanding the use of Distribute By will help us effectively
parallelize the jobs with evenly distributed data. Now assume we are
joining datasets based on a join key and the key is unordered in both
datasets. In this case, Spark has to shuffle the data between the nodes
to get the rows from both tables with the same key. See Figure [7-6](#469054_1_En_7_Chapter.xhtml#Fig6)  .

![../images/469054_1_En_7_Chapter/469054_1_En_7_Fig6_HTML.png](images/469054_1_En_7_Chapter/469054_1_En_7_Fig6_HTML.png)

Figure 7-6
Shuffling in
Spark during joins

You can see from the Figure [7-6](#469054_1_En_7_Chapter.xhtml#Fig6) that there is a
lot of data moving around between the nodes. This is called
*shuffling* across the nodes and reducing such shuffling will
help us improve the performance of the jobs .

We just saw that Distribute By is one such option. Let’s try to
visualize Distribute By. Assuming there are two partitions, we would get
Figure [7-7](#469054_1_En_7_Chapter.xhtml#Fig7).

![../images/469054_1_En_7_Chapter/469054_1_En_7_Fig7_HTML.png](images/469054_1_En_7_Chapter/469054_1_En_7_Fig7_HTML.png)

Figure 7-7
Using Distribute By

In Figure [7-7](#469054_1_En_7_Chapter.xhtml#Fig7), note that even
though you have efficiently parallelized the join, there is still a lot
of shuffling. Note that from the subjects that the data is going
everywhere. Such data shuffling leads to performance degradation and out
of memory errors when sufficient memory is not available.
The way to minimize data shuffling is by sorting the data in subjects
and then joining it with students .
In the following query, we are applying both the Distribute By and
Sort By clauses to achieve maximum performance .

Here are the queries where we are preparing the distributed and sorted
`students` and
`subjects` data.

```
>>> student_distributed_sorted = spark.sql("select * from StudentsDob distribute by studentId  SORT BY studentId")
>>> subjects_distributed_sorted = spark.sql("select * from subjects distribute by studentId SORT BY studentId")
>>> student_distributed_sorted.createOrReplaceTempView("student_distributed_sorted")
>>> subjects_distributed_sorted.createOrReplaceTempView("subjects_distributed_sorted")
```

Let’s look at the joined data output:

```
>>> spark.sql("select st.studentid,name,subject,marks from student_distributed_sorted st left join subjects_distributed_sorted sb on (st.studentId = sb.studentId)").show()
```

Let’s try to visualize the join. See Figure [7-8](#469054_1_En_7_Chapter.xhtml#Fig8).

![../images/469054_1_En_7_Chapter/469054_1_En_7_Fig8_HTML.png](images/469054_1_En_7_Chapter/469054_1_En_7_Fig8_HTML.png)

Figure 7-8
Visualizing the join

In Figure [7-8](#469054_1_En_7_Chapter.xhtml#Fig8), note that the
number of data shuffles has been reduced and the flow of data is very
smooth. There isn’t much shuffling happening compared to Figure [7-7](#469054_1_En_7_Chapter.xhtml#Fig7).

The output looks very similar except that you have optimized the query
in a better way :

```
+---------+-------+-------+-----+
|studentid|   name|subject|marks|
+---------+-------+-------+-----+
|      si2|  Maria|   Java|   83|
|      si2|  Maria| Python|   85|
|      si4|    Bob| Python|   84|
|      si4|    Bob|    C++|   78|
|      si3|  Julie|   Ruby|   72|
|      si3|  Julie|   Java|   76|
|      si6|William|   Java|   70|
|      si1|  Robin|   Java|   81|
|      si1|  Robin| Python|   75|
+---------+-------+-------+-----+
```

Now that you have used the Distribute By and Sort By clauses
together, Spark By provides an optional clause to combine the data—it’s
called Cluster By.
Using Cluster By is the same as using Distribute By and Sort By
together on the same columns. Let’s see Cluster By in action.

Here are the queries using the Cluster By clause :

```
>>> student_distributed_clustered = spark.sql("select * from StudentsDob cluster by studentId ")
>>> subjects_distributed_clustered = spark.sql("select * from subjects cluster by studentId ")
>>>student_distributed_clustered.createOrReplaceTempView("student_distributed_clustered")
>>>subjects_distributed_clustered.createOrReplaceTempView("subjects_distributed_clustered")
>>> spark.sql("select st.studentid,name,subject,marks from student_distributed_clustered st left join subjects_distributed_clustered sb on (st.studentId = sb.studentId)").show()
```

Here is the output:

```
+---------+-------+-------+-----+
|studentid|   name|subject|marks|
+---------+-------+-------+-----+
|      si2|  Maria|   Java|   83|
|      si2|  Maria| Python|   85|
|      si4|    Bob| Python|   84|
|      si4|    Bob|    C++|   78|
|      si3|  Julie|   Ruby|   72|
|      si3|  Julie|   Java|   76|
|      si6|William|   Java|   70|
|      si1|  Robin|   Java|   81|
|      si1|  Robin| Python|   75|
+---------+-------+-------+-----+
```

In this recipe, you have successfully used the Distribute By, Sort
By, and Cluster By clauses to effectively optimize your PySpark SQL
queries .

8. Structured Streaming

In this chapter, we look into Apache Spark’s structured streaming
feature. So far, we have seen the fluent APIs Apache Spark provides for
batch processing data. Typical ETL-based data flows are batch oriented
and operate on static data. In this case, this static data has been
obtained and is available for processing. While this is one side of the
coin, the other side is streaming data. For example, websites such as
Twitter and Facebook are continuously fed with data from their billions
of users. This data is coming into these websites at a rapid rate. If
the websites need to identify the most talked about topic for the past
hour, for example, they have to act very fast. They will not have time
to consolidate the data, store it, and then apply ETL processing to it.
Rather, these websites need to apply the analytics on the fly while the
data is coming into the system. Such processing is called  *stream computation*
.
These kinds of computations are very helpful in scenarios such as
monitoring and diagnosis, identifying trends in the data, and especially
in the IoT world where there is an ever-flowing stream of data from
which we have to predict device malfunction, for example. Apache Spark
provides streaming APIs that enable you to receive such streaming data
and apply analytics over it. This is a very interesting topic; let’s get
into the recipes.

As part of structured streaming, we are going to discuss the following
recipes:

*   Recipe 8-1\. Set up a streaming DataFrame on a directory

*   Recipe 8-2\. Initiate a streaming query and see it in
    action

*   Recipe 8-3\. Apply PySparkSQL on streaming

*   Recipe 8-4\. Join streaming data with static data

Recipe 8-1\. Set Up a Streaming DataFrame on
a Directory

Problem
You want to create a streaming DataFrame on a
directory.

Solution

As part of its structured streaming feature, Spark provides multiple
streaming options. They are called *sources* in Spark terminology.

*   *File
    source* *:* This is essentially when you get the
    streaming data as incremental files. Spark supports file formats such
    txt, csv, json, and parquet. It is very similar to creating a DataFrame
    from a static file. But with a File source, you will create a stream on
    a directory into which you are expecting the incremental files to be
    placed. You might wonder how there will be incremental files, when Kafka
    and similar tools are used. In the enterprise world that uses a wide
    variety of technologies, including legacy software such as mainframes
    etc., those systems will provide you with data in the form of files. So
    it is essential to consider incremental files as a source of streaming.
    We will delve more into that later.

*   *Kafka
    source* *:* Kafka is the distributed streaming
    platform that has become de facto in the Big Data world. Kafka is a
    messaging system that contains a message broker supporting topics. These
    topics act as message producers, which in turn can be consumed by
    consumers.

*   *Socket
    source*  *and*  *Rate source*
    *:* These sources are mainly available for testing
    purposes. Socket sources can read data from socket connections. For
    example, you can connect Apache Spark to any particular socket to
    consume the streaming data coming to the socket. Similarly, Rate sources
    can be use to generate rows at any frequency. They contain timestamps
    and values where the timestamp is the time at which the row was
    generated and the value is a long containing a message count.

Let’s go ahead and create DataFrame streaming from
a File source.

For this example, I will be using a smaller set of temperature data for
easier understanding. The data contains just two columns—`day` and `tempInCelsius`. Both
are string types. The `day` column will
contain the day for which the temperature is obtained. `tempInCelsius` denotes
the observed temperature for that day. See Figure [8-1](#469054_1_En_8_Chapter.xhtml#Fig1).

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig1_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig1_HTML.jpg)

Figure 8-1
Temperature
dataset

This data has seven rows, with the header row containing the column
names.

How It Works
First and foremost, we need to create a schema and assign it to the
newly created DataFrame.

Use the following code to create a schema.

```
>>> from pyspark.sql.types import StructType
>>> temperatureSchema = StructType().add("day", "string").add("tempInCelsius", "string")
```

As you see in the code, you need to first import the `StructType`. Then you
need to create a struct type with the necessary column along with its
data type, as shown.

Let’s try to print the content of the schema:

```
>>> temperatureSchema.simpleString()
```

Here is the output. We are able to see both the columns just added in
the schema. Now you are good to go to the next step.

```
'struct'
```

The next step is to create the DataFrame from streaming. Let’s see the
code for creating the DataFrame.

```
>>> temperature_streaming_df = spark \
... .readStream \
... .option("sep", ",") \
... .schema(userSchema) \
... .csv("temperature-data")
```

There are multiple things going on in this code. Let’s examine them
one by one.
Initially we are calling the method `readStream`. This
method is essential to creating the streaming. This method returns a
`DataStreamReader` used
to read streaming data as a DataFrame.
Next, we are setting the separator to `,` (comma). This is
because we are using a comma-separated value.

To make sure the DataFrame is in streaming mode, we use the `isStreaming` method
shown here.

```
>>> temperature_streaming_df.isStreaming
```

Here is the output telling us that the DataFrame is streaming:

```
True
```

Then, using the `schema` method, we are
setting the schema for the DataFrame. Finally, we are specifying the
directory onto which Spark should bind and look for streaming data.
Let’s try to print the schema and then register the DataFrame as a
table or view and then show the contents of the DataFrame. Initially I
have kept the directory empty. So you may not see any output from the
DataFrame.

Printing the schema

```
>>> temperature_streaming_df.printSchema()
```

Here is the output. The output is as expected, with two given columns.

```
root
|-- day: string (nullable = true)
|-- tempInCelsius: string (nullable = true)
```

Now we register the DataFrame as a table.

```
>>> temperature_streaming_df.createOrReplaceTempView("temperature_streaming_df")
```

I am going to apply a simple SQL query to show the contents of the
table.

Let’s print the contents of the DataFrame.

```
>>> spark.sql("select * from temperature_streaming_df").show()
```

Alas, you are seeing a lot of exceptions with this final message.

```
pyspark.sql.utils.AnalysisException: 'Queries with streaming sources must be executed with writeStream.start();;\nFileSource[temperature-data]'
```

Don’t worry, I wanted you to understand what is going on so that you
are able to solve such issues when you are doing it by yourself.
Spark streaming doesn’t start unless you call the start method on the
output sink. We will look at setting the sink in the next recipe. In
this recipe, we covered Spark Structured streaming, as well as setting
up a streaming DataFrame from a directory and registering it as a
table.

Recipe 8-2\. Initiate a Streaming Query and See It in
Action

Problem
You want to persist streaming data into a file.

Solution
As you saw in the previous recipe, unless you start a sink on a
streaming DataFrame, the streaming is not really active. Let’s start
with a simple streaming scenario and then move ahead step by step. To
start with simple streaming, you need to understand some concepts.

*Sinks* are where you write your output data. There are multiple
sink types that Spark supports—File, Kafka, Console, foreach, and
Memory. We will look into the File, Kafka, and Console sink types.

*   *File
    sink* *:* The output is stored in a specified
    directory.

*   *Kafka
    sink* *:* Output is stored in Kafka topics. It could
    be more than one topic and multiple consumers can be listening to it for
    various purposes. For example, one topic could be for further processing
    and another one could be for persisting the data into Hadoop for
    archiving or for future references.

*   *Console
    sink* *:* Used for debugging purposes to print the
    data into the console.

Let’s look at the output modes. The output mode defines how to write the
data into the sink. Spark supports three kinds of output modes—Append,
Complete, and Update.

*   *Append:* This is the default mode that simply appends the
    incoming stream of data into the sink.

*   *Complete:* This mode writes the data completely into the
    sink from in-memory. This is needed when there are aggregation queries
    performed on the streaming data. For example, if you need to determine
    which political leaders have maximum mentions in the news for the last
    15 minutes, and so on.

*   *Update:* Only the updated rows are updated into the
    sink.

Now that you understand the output modes and sinks concepts, we will
start the streaming and see these concepts in action.
To start, we are going to write the streamlining data into the
console so you can easily see the output and understand what is going
on.

I will be using four files for illustration purposes, as shown in
Figures [8-2](#469054_1_En_8_Chapter.xhtml#Fig2) through [8-5](#469054_1_En_8_Chapter.xhtml#Fig5).

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig5_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig5_HTML.jpg)

Figure 8-5
tempData-4.csv

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig4_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig4_HTML.jpg)

Figure 8-4
tempData-3.csv

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig3_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig3_HTML.jpg)

Figure 8-3
tempData-2.csv

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig2_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig2_HTML.jpg)

Figure 8-2
tempData-1.csv

How It Works

Be sure to observe the contents of the files carefully.

```
>>> from pyspark.sql.types import StructType
>>> temperatureSchema = StructType().add("day", "string").add("tempInCelsius", "string")
>>> temperature_streaming_df = spark \
... .readStream \
... .option("sep", ",") \
... .schema(temperatureSchema) \
... .csv("C:\\book-author\\data\\data\\temperature-data")
```

With this code, we have created a streaming DataFrame. Now we are
going to start it using the `writeStream`
method .

The following code starts the streaming. This is a very simple form of
streaming, where we create a stream using the `writeStream` method,
which will simply write into the console and will keep on appending the
incoming data.

```
>>> query = temperature_streaming_df \
... .writeStream \
... .format("console") \
... .outputMode("append") \
... .start() \
```

So far you will not see any output. Now we place the `tempData.csv` file
into the `temperature-data`
directory. Notice what happens then.

You can observe the following output in the console.

```
-------------------------------------------
Batch: 0
-------------------------------------------
+----+-------------+
| day|tempInCelsius|
+----+-------------+
|day1|         12.2|
|day2|         13.1|
|day3|         12.9|
|day4|         11.9|
|day5|           14|
|day6|         13.9|
|day7|         12.7|
+----+-------------+
```

Now we place the `tempData-3.csv` file.
Here is the output:

```
-------------------------------------------
Batch: 1
-------------------------------------------
+-----+-------------+
|  day|tempInCelsius|
+-----+-------------+
| day8|         12.2|
| day9|         13.1|
|day10|         12.9|
|day11|         11.9|
|day12|           14|
|day13|         13.9|
|day14|         12.7|
+-----+-------------+
```

Notice from the output that the `tempData-3.csv` file
is printed to the console.
Now for the interesting part. Let’s now place the `tempData-2.csv` file.
You might have noticed that the `tempData-2.csv` and
`tempData-1.csv`
files contain the same data. So ideally they are duplicates. In this
scenario, you will not see any output in the console. You might wonder
why you are not seeing any output, and this can be easily understood
from the next action.
Between `tempData-2.csv` and
`tempData-4.csv`,
I made a small change to `day7`’s temperature
(it’s now 12.7 instead of the original 12.6). With that one change, you
are able to see the output in the console.

Spark provides an *exactly-once* guarantee, which means that each
record will be processed exactly once. None of the data will be lost in
the process and none of the data will be processed more than once. This
is a very strong feature that Spark provides compared to other streaming
solutions.

```
-------------------------------------------
Batch: 2
-------------------------------------------
+----+-------------+
| day|tempInCelsius|
+----+-------------+
| day|tempInCelsius|
|day1|         12.2|
|day2|         13.1|
|day3|         12.9|
|day4|         11.9|
|day5|           14|
|day6|         13.9|
|day7|         12.8|
+----+-------------+
```

Now we are going to see the streaming DataFrame represented as an
in-memory table so that we can directly apply PySparkSQL queries to
it.

Here is the code:

```
>>> query = temperature_streaming_df \
... .writeStream \
... .format("memory") \
... .queryName("TemperatureData") \
... .outputMode("append") \
... .start()
```

You can see that we made two changes compared to the previous
execution of this statement. First, we changed the format from `console` to `memory`. This is to
signify that instead of writing the output to the console, we are asking
Spark to save the data in memory.

Next, we call a new method called `queryName` and provide
`TemperatureData`
as its input. This will create a table representation over the in-memory
data. Here are the contents of the table using PySparkSQL:

```
>>> spark.sql("select * from TemperatureData").show()
```

Here is the output. You can see that it is empty. It is because we have
cleared all the data in the directory.

```
+---+-------------+
|day|tempInCelsius|
+---+-------------+
+---+-------------+
```

Now we add the `tempData-1.csv` and
`tempData-3.csv`
files into the directory. Let’s look at the output after adding both the
files.

```
>>> spark.sql("select * from TemperatureData").show()
```

Here is the output:

```
+-----+-------------+
|  day|tempInCelsius|
+-----+-------------+
| day8|         12.2|
| day9|         13.1|
|day10|         12.9|
|day11|         11.9|
|day12|           14|
|day13|         13.9|
|day14|         12.7|
| day1|         12.2|
| day2|         13.1|
| day3|         12.9|
| day4|         11.9|
| day5|           14|
| day6|         13.9|
| day7|         12.7|
+-----+-------------+
```

Recipe 8-3\. Apply PySparkSQL on Streaming

Problem
You want to apply SQL filters on the streaming data.

Solution
We need to apply a filter to the same table.

How It Works

Here is the query where we filter for days with a `tempInCelsius` greater
than 13\. Since the `tempInCelsius` column
is of string type, we cast it to `double`.

```
>>> spark.sql("select * from TemperatureData where cast(tempInCelsius as double) > 13").show()
```

Here is the output:

```
+-----+-------------+
|  day|tempInCelsius|
+-----+-------------+
| day9|         13.1|
|day12|           14|
|day13|         13.9|
| day2|         13.1|
| day5|           14|
| day6|         13.9|
+-----+-------------+
```

So with Spark’s structured streaming, we can consider it a table and
Spark provides us with data-wrangling capabilities with the ease of
SQLs. Similarly, we can perform any SQL operations on this table. You
may want to join this data with static data to be able to map the
incoming data to apply analytics and get trend reports on specific aspects.

Recipe 8-4\. Join Streaming Data with Static
Data

Problem
You need to join streaming data with static data.

Solution
In this recipe, you need to join streaming data with static data to
get more information from the streaming data. I have modified the data a
little bit to be able to complete this recipe.

Figure [8-6](#469054_1_En_8_Chapter.xhtml#Fig6) shows a snapshot
of the static data used in this recipe.

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig6_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig6_HTML.jpg)

Figure 8-6
Static data

The data in Figure [8-6](#469054_1_En_8_Chapter.xhtml#Fig6) is static data
that represents `ZipCode` and `City`. For
illustration purposes, I use eight cities.

The streaming data is going to be temperature data that will come in
every minute for each of the cities. Take a look at the temperature data
snapshot in Figures [8-7](#469054_1_En_8_Chapter.xhtml#Fig7) through [8-9](#469054_1_En_8_Chapter.xhtml#Fig9).

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig9_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig9_HTML.jpg)

Figure 8-9
4 zipcode-temp-3.csv file

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig8_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig8_HTML.jpg)

Figure 8-8
The 4 zipcode-temp-2.csv file

![../images/469054_1_En_8_Chapter/469054_1_En_8_Fig7_HTML.jpg](images/469054_1_En_8_Chapter/469054_1_En_8_Fig7_HTML.jpg)

Figure 8-7
The 4 zipcode-temp-1.csv file

As you can see in these figures, these are the three files I am going to
stream. Each file contains three columns:

*   `time`—Time at which
    the reading was taken

*   `zipCode`—ZIP code for
    which the reading was taken

*   `tempInCelsius`—Temperature
    reading in degrees Celsius

How It Works

Step 8-4-1\. Creating a Static Table Over the Zip
Code City Data File

The following code creates that table.

```
>>> city_zipcode_df = spark.read.csv('city-zipcode.csv',header=True, inferSchema=True)
>>> city_zipcode_df.createOrReplaceTempView("CityZipcode")
>>> spark.sql("select * from CityZipcode").show()
```

Here is the output:

```
+-------+------------+
|Zipcode|        City|
+-------+------------+
|   8817|      EDISON|
|  10801|NEW ROCHELLE|
|   8053|     MARLTON|
|  15554|   NEW PARIS|
|  45874|   OHIO CITY|
|  45347|   NEW PARIS|
|  59547|      ZURICH|
|  66101| KANSAS CITY|
+-------+------------+
```

Step 8-4-2\. Creating a Streaming DataFrame for
the Temperate Data for ZIP Code Files

In the following code, we are creating the schema and then using it to
create a streaming DataFrame.

```
>>> temperatureSchema = StructType().add("time", "timestamp").add("ZipCode", "string").add("tempInCelsius", "double")
>>> temperature_streaming_df = spark \
... .readStream \
... .option("sep", ",") \
... .schema(temperatureSchema) \
... .csv("zipcode-temp")
```

Step 8-4-3\. Join Static Data with Streaming
Data

Now we are ready to apply the join. Let’s apply the
join using PySparkSQL.

```
>>> tempDF = spark.sql("select City, T.zipCode,tempInCelsius from temperature_streaming T JOIN CityZipcode C on (C.ZipCode = T.Zipcode)")
```

Here, we are joining both tables we created:

```
>>> query = (
...   tempDF
...     .writeStream
...     .format("console")
...     .outputMode("append")
...     .start()
... )
```

Now we place the files one by one. Upon placing the `zipcode-temp-1.csv`
file, here is the output at the console:

```
>>> ---------------------------------------
Batch: 0
-------------------------------------------
+------------+-------+-------------+
|        City|zipCode|tempInCelsius|
+------------+-------+-------------+
|      EDISON|   8817|         12.1|
|NEW ROCHELLE|  10801|         13.9|
|     MARLTON|   8053|         11.7|
|   NEW PARIS|  15554|         13.0|
|   OHIO CITY|  45874|         13.3|
|   NEW PARIS|  45347|         12.4|
|      ZURICH|  59547|         10.5|
| KANSAS CITY|  66101|         10.6|
+------------+-------+-------------+
```

Upon placing the `zipcode-temp-2.csv`
file, here is the output at the console:

```
-------------------------------------------
Batch: 1
-------------------------------------------
+------------+-------+-------------+
|        City|zipCode|tempInCelsius|
+------------+-------+-------------+
|      EDISON|   8817|         12.1|
|NEW ROCHELLE|  10801|         13.9|
|     MARLTON|   8053|         11.7|
|   NEW PARIS|  15554|         13.0|
|   OHIO CITY|  45874|         13.3|
|   NEW PARIS|  45347|         12.4|
|      ZURICH|  59547|         10.5|
| KANSAS CITY|  66101|         10.6|
+------------+-------+-------------+
```

Finally, upon placing the `zipcode-temp-2.csv`
file, here is the output at the console:

```
-------------------------------------------
Batch: 2
-------------------------------------------
+------------+-------+-------------+
|        City|zipCode|tempInCelsius|
+------------+-------+-------------+
|      EDISON|   8817|         14.5|
|NEW ROCHELLE|  10801|         12.9|
|     MARLTON|   8053|         11.6|
|   NEW PARIS|  15554|         10.0|
|   OHIO CITY|  45874|         13.6|
|   NEW PARIS|  45347|         11.3|
|      ZURICH|  59547|         10.2|
| KANSAS CITY|  66101|         11.6|
+------------+-------+-------------+
```

You can observe that the join between `temperature_streaming`
and `CityZipcode`
is realized at the console output automatically. This is the power of
Spark streaming. Instead of printing the output to the console, you can
easily persist it into a file or keep it in memory and further apply
analytics. With this chapter, you are able to successfully use Spark
structured streaming to use process streaming data.

9. GraphFrames

GraphFrames are an abstraction of DataFrames that are used to do
Graph Analytics. Graph Analytics stems from the mathematical Graph
Theory. Graph Theory is a very important theory used to represent
relationships between entities, which we can use to perform various
analyses. You are using Graph Theory in your everyday life when using
Google. Google introduced the PageRank algorithm that
is based on Graph Theory. It tries to identify the most influential
website that suits your search in the best way.
While Graph Theory is used in various sciences, computer science also
tends to solve a lot of problems with Graph Theory. Some of the
applications of Graph Theory include social media problems, travel, chip
design, and many other fields. In fact, every time you run a Spark job,
you are using Graph Theory. Spark uses Directed Acyclic Graphs
to represent an RDD. It uses it to find the optimized plan to your
query.

Graphs are a combination of vertices that are connected to each other
using edges. While vertices can be thought of as nodes or entities,
edges represent the relationship between these entities. Figure [9-1](#469054_1_En_9_Chapter.xhtml#Fig1) visualizes a
graph of a family.

![../images/469054_1_En_9_Chapter/469054_1_En_9_Fig1_HTML.png](images/469054_1_En_9_Chapter/469054_1_En_9_Fig1_HTML.png)

Figure 9-1
Graphs are a combination of vertices

The diagram in Figure [9-1](#469054_1_En_9_Chapter.xhtml#Fig1) represents a
small family of four members—the husband (Andrew), the wife (Sierra), a
son (Bob), and a daughter (Emily). People are represented as nodes,
which are called *vertices*. Each person is connected to the
other. You can observe that just within a four-member family, there are
12 relationships. These relationships are represented using the edges
that connect them. Now imagine a social media app, such as LinkedIn,
that needs to connect millions of people. There is going to be an
enormous number of edges. To be able to apply analytics on this kind of
data, regular databases will not suffice. With a regular database, you
would need to apply self-joins so many times and applying self-joins
will literally bring your database down.
Graph Theory solves complex issues like this. Spark provides
GraphFrames to represent this graph data. In this chapter, we will learn
how to create GraphFrames and apply some of the most used algorithms to
solve complex problems.

In this chapter, we are going to discuss the following recipes:

*   Recipe 9-1\. Create GraphFrames

*   Recipe 9-2\. Apply triangle counting in a GraphFrame

*   Recipe 9-3\. Apply the PageRank algorithm

*   Recipe 9-4\. Apply the Breadth First algorithm

Recipe 9-1\. Create GraphFrames

Problem
You need to create a GraphFrame from
a given dataset.

Solution

This solution uses family relationship data. We are going to use two
datasets. Figures [9-2](#469054_1_En_9_Chapter.xhtml#Fig2) and [9-3](#469054_1_En_9_Chapter.xhtml#Fig3) show the
datasets.

![../images/469054_1_En_9_Chapter/469054_1_En_9_Fig3_HTML.jpg](images/469054_1_En_9_Chapter/469054_1_En_9_Fig3_HTML.jpg)

Figure 9-3
relationship.csv

![../images/469054_1_En_9_Chapter/469054_1_En_9_Fig2_HTML.jpg](images/469054_1_En_9_Chapter/469054_1_En_9_Fig2_HTML.jpg)

Figure 9-2
persons.csv

This is a convenient dataset for everyone to use and understand.
Let’s load this dataset.

How It Works
Before starting with GraphFrames, you need to install GraphFrames on
your machine.

If you have not installed GraphFrames and you start using it, you will
receive the following error message.

```
>>> from graphframes import *
```

Here is the output:

```
Traceback (most recent call last):
File "", line 1, in 
ModuleNotFoundError: No module named 'graphframes'
```

You need to first install GraphFrames using the
following command from `pyspark`.

```
pyspark --packages graphframes:graphframes:0.6.0-spark2.3-s_2.11
```

Run this command in your command prompt. PySpark will automatically
download the GraphFrame package and install it. Once you install it, you
can check it by importing the GraphFrame package, as shown here.

```
>>> from graphframes import *
```

If you don’t see the error message anymore, you can use GraphFrame
successfully.

Let’s now load the `persons.csv` and `relationship.csv` data
as DataFrames.

```
>>> personsDf = spark.read.csv('persons.csv',header=True, inferSchema=True)
>>> personsDf.createOrReplaceTempView("persons")
```

This command loads the data into the `personsDf`
DataFrame  and
creates a temporary view of it. Let’s see the content of this view.

```
>>> spark.sql("select * from persons").show()
```

Here is the output:

```
+--+-------+---+
|id|   Name|Age|
+--+-------+---+
| 1| Andrew| 45|
| 2| Sierra| 43|
| 3|    Bob| 12|
| 4|  Emily| 10|
| 5|William| 35|
| 6| Rachel| 32|
+--+-------+---+
```

Now we load the relationship data as
well. Here is the code and the output:

```
>>> relationshipDf = spark.read.csv('relationship.csv',header=True, inferSchema=True)
>>> relationshipDf.createOrReplaceTempView("relationship")
>>> spark.sql("select * from relationship").show()
+---+---+--------+
|src|dst|relation|
+---+---+--------+
|  1|  2| Husband|
|  1|  3|  Father|
|  1|  4|  Father|
|  1|  5|  Friend|
|  1|  6|  Friend|
|  2|  1|    Wife|
|  2|  3|  Mother|
|  2|  4|  Mother|
|  2|  6|  Friend|
|  3|  1|     Son|
|  3|  2|     Son|
|  4|  1|Daughter|
|  4|  2|Daughter|
|  5|  1|  Friend|
|  6|  1|  Friend|
|  6|  2|  Friend|
+---+---+--------+
```

Now that both the datasets are available, we are going to import the
GraphFrames.

```
>>>from graphframes import *
>>>
Graphframes has been successfully loaded.
```

The following command creates your first GraphFrame. The GraphFrame
accepts two DataFrames as inputs—vertices and edges. GraphFrames like to
have a naming convention in the column name, which you need to follow.
Those rules are
defined as follows:

*   A DataFrame that represents vertices should contain a column
    named `id`. Here,
    `personsDf`
    contains a column name `id`.

*   A DataFrame that represents edges should contain columns named
    `src` and `dst`. Here, `reationshipDf`
    contains the columns `src` and `dst`.

```
>>> graph = GraphFrame(personsDf, relationshipDf)
```

Let’s try to see the type of the `graph` variable. Here
it is:

```
>>> graph
GraphFrame(v:[id: int, Name: string ... 1 more field], e:[src: int, dst: int ... 1 more field])
```

So it is a GraphFrame that contains `v` and `e`. The `v` represents vertices
and `e`
represents edges.
Now that you have successfully created a GraphFrame, it is important
to understand degrees.
 *Degrees*
 represent the number of edges that are connected to a vertex.
GraphFrame supports 
*inDegrees*  and *outDegrees*. inDegrees give you
the number of incoming links to a vertex. outDegrees give the
number of outgoing edges from a node. It is very important to understand
this. Let’s try to see the output for a given example.
Here you are going to find all the edges connected to Andrew.

The following code gets all the links and filters based on a given
filter, which is set by `id = 1`:

```
>>> graph.degrees.filter("id = 1").show()
```

Here is the output. Note that there are 10 entries for Andrew (`id=1`) in the
relationship dataset.

```
+--+------+
|id|degree|
+--+------+
| 1|    10|
+--+------+
```

The following code gets the number of incoming links to Andrew. This is
obtained by calling the 
`inDegrees`
 method.

```
>>> graph.inDegrees.filter("id = 1").show()
```

Here is the output. There are five entries for Andrew in the `dst` column.

```
+--+--------+
|id|inDegree|
+--+--------+
| 1|       5|
+--+--------+
```

The following code shows how to get the number of links coming out from
Andrew using the  `outDegrees` 
method .

```
>>> graph.outDegrees.filter("id = 1").show()
```

Here is the output. There are five relationship entries for `src` column value
`1`.

```
+--+---------+
|id|outDegree|
+--+---------+
| 1|        5|
+--+---------+
```

With this, you have successfully created a GraphFrame from the
vertices and edges dataset.
Apache Spark provides multiple Graph algorithms built-in. These
algorithms are abstracted and provided as easy-to-use APIs. Once you
prepare a GraphFrame, any of the following algorithms can be used with
just a method.

GraphFrame provides the following built-in algorithms:

*   Connected components

*   Label propagation

*   PageRank

*   SVD++

*   Shortest Path

*   Strongly connected components

*   Triangle count

Recipe 9-2\. Apply Triangle Counting in
a GraphFrame

Problem
You need to find the triangle count value for
each vertex.

Solution
GraphFrames provide an easy-to-use triangleCount API, which upon
calling on a given GraphFrame, outputs a DataFrame with a count column
added to each of the vertex rows. This count column identifies how many
triangle relationships the vertex is participating in. Now we will see
how to get the triangle count for each vertex. This is very helpful with
route-finding
problems and places an important role on the PageRank
algorithm.

How It Works

The following code is used to find the triangle count for each vertex.

```
>>> graph.triangleCount().show()
```

Here is the output:

```
+-----+---+-------+---+
|count| id|   Name|Age|
+-----+---+-------+---+
|    3|  1| Andrew| 45|
|    1|  6| Rachel| 32|
|    1|  3|    Bob| 12|
|    0|  5|William| 35|
|    1|  4|  Emily| 10|
|    3|  2| Sierra| 43|
+-----+---+-------+---+
```

A new column count is added in the output that represents the
triangle count. The output shows that Andrew and Sierra have the maximum
triangle counts, since they are involved in three kinds of
relationships. Andrew as father, friend, and husband and Sierra as
mother, friend, and wife. With this, you have successfully created a
GraphFrame and applied analytics to it.
You can also register the output of the triangleCount output
DataFrame as a table and easily apply a query on that. Let’s use
PySparkSQL to identify all the people with a maximum triangle count.
Using the following code, you can find the people in the family with
the highest triangle counts.
After that, you can join it with the `Persons` DataFrame to
be able to view the person’s details.

The following code does exactly that. Although you can achieve this
result with a programmatic method in one line of code, the following
method uses simple SQL commands.

```
>>> personsTriangleCountDf.createOrReplaceTempView("personsTriangleCount")
>>> maxCountDf = spark.sql("select max(count) as max_count from personsTriangleCount")
>>> maxCountDf.createOrReplaceTempView("personsMaxTriangleCount")
>>> spark.sql("select * from personsTriangleCount P JOIN (select * from personsMaxTriangleCount) M ON (M.max_count = P.count) ").show()
```

Here is the output:

```
+-----+---+------+---+---------+
|count| id|  Name|Age|max_count|
+-----+---+------+---+---------+
|    3|  1|Andrew| 45|        3|
|    3|  2|Sierra| 43|        3|
+-----+---+------+---+---------+
```

Recipe 9-3\. Apply a PageRank Algorithm

Problem
You need to apply PageRank algorithms to
find the most influential person in this family.

Solution
The PageRank algorithm was the base of Google during its initial
period. It was originally started by Google’s founders to identify the
most important pages on the Internet. It uses the idea that the most
important pages are linked to by other pages most often. Also, it uses
the idea that the higher the link to a given page from higher ranked
pages, the more important the page. Thus, Google uses linked web pages
represented in graph form to identify important pages for us.
The PageRank algorithm measures the importance of each vertex in a
graph. Assume a scenario where one Twitter user has 10 important
followers, and each of those followers has multiple followers in turn.
That Twitter user gets a higher ranking compared to a Twitter user with
50 “normal” followers. This is to say that the PageRank algorithm
considers each important follower a legitimate endorsement of the
Twitter user and thereby gives a higher ranking to the user.

How It Works

Let’s see the PageRank algorithm in action. Apache Spark makes it
very simple to call the PageRank algorithm.

```
>>> pageRank = graph.pageRank(resetProbability=0.20, maxIter=10)
```

Here, we are calling the PageRank algorithm using the `pageRank` method . It takes two attributes:

*   `resetProbablity`: This
    value is a random value reset probability
    (alpha).

*   `maxIter` : This
    is the number of times you want `pageRank` to
    run.

Let’s look at `pageRank`. It is a
GraphFrame containing the vertices and edges attributes.

```
>>> pageRank
GraphFrame(v:[id: int, Name: string ... 2 more fields], e:[src: int, dst: int ... 2 more fields])
```

Let’s look at the `vertices`
attribute :

```
>>> pageRank.vertices.printSchema()
root
|-- id: integer (nullable = true)
|-- Name: string (nullable = true)
|-- Age: integer (nullable = true)
|-- pagerank: double (nullable = true)
```

You can see from the original `persons` schema that a
new column has been added called `pagerank`. This column
is added by Spark and indicates the `pageRank` score for
the vertex.

Similarly, let’s look at the `edges` present in this
GraphFrame.

```
>>> pageRank.edges.printSchema()
root
|-- src: integer (nullable = true)
|-- dst: integer (nullable = true)
|-- relation: string (nullable = true)
|-- weight: double (nullable = true)
```

As you can see from the schema, a new column weight has been added to
the original relationship schema. This column weight indicates the edge
weight that contributed to the PageRank score.
Let’s look at the PageRank score for each vertex and the weight for
each of the edges.
We are going to order the PageRank in descending order so that we can
see the most connected person in the family based on the links with the
other family members.

The following code lists all the vertices in descending order.

```
>>> pageRank.vertices.orderBy("pagerank",ascending=False).show()
```

Let’s see the output:

```
+--+-------+----+------------------+
|id|   Name| Age|          pagerank|
+--+-------+----+------------------+
| 1| Andrew| 45 | 1.787923121897472|
| 2| Sierra| 43 | 1.406016795082752|
| 6| Rachel| 32 |0.7723665979473922|
| 4|  Emily| 10 |0.7723665979473922|
| 3|    Bob| 12 |0.7723665979473922|
| 5|William| 35 |0.4889602891776001|
+--+-------+----+------------------+
```

You can see from this output that Andrew is the most connected
person.

Let’s look at the weight contributed by each of the edges. Here we are
listing all the edges in descending order so that the maximum weight is
listed first.

```
>>> pageRank.edges.orderBy("weight",ascending=False).show()
```

Here is the output. You can identify from the output that the edge 5, 1,
Friend gets the maximum weightage. William’s relationship with Andrew
gets the maximum weight since it is unique. No one other than Andrew is
a friend to William.

```
+---+---+--------+------+
|src|dst|relation|weight|
+---+---+--------+------+
|  5|  1|  Friend|   1.0|
|  3|  1|     Son|   0.5|
|  4|  1|Daughter|   0.5|
|  4|  2|Daughter|   0.5|
|  6|  1|  Friend|   0.5|
|  3|  2|     Son|   0.5|
|  6|  2|  Friend|   0.5|
|  2|  3|  Mother|  0.25|
|  2|  4|  Mother|  0.25|
|  2|  1|    Wife|  0.25|
|  2|  6|  Friend|  0.25|
|  1|  2| Husband|   0.2|
|  1|  6|  Friend|   0.2|
|  1|  3|  Father|   0.2|
|  1|  4|  Father|   0.2|
|  1|  5|  Friend|   0.2|
+---+---+--------+------+
```

Now that you understand the PageRank algorithm and have applied it to
GraphFrames, let’s move on to the Breadth First algorithm.

Recipe 9-4\. Apply the Breadth First Algorithm

Problem
You need to apply the Breadth First algorithm
to find the shortest way to connect to a person.

Solution
You might have often noticed LinkedIn telling you how far you are
from any new user. For example, you will notice that a user whom you
would like to connect to is a second connection or a third connection.
This tells you that you are two vertices away from the vertex from where
you are looking. This is one way of identifying how far a vertex is to
another vertex.
Similarly, in scenarios where flight companies need to identify the
shortest path between cities, they need to identify the path with the
least number of vertices between airports. Of course, there may be
additional conditions, such as time, whether stops are required on the
course, etc.
Problems very similar to these exist in every industry. For example,
chip-designing companies need to identify the shortest circuitry path;
telecom companies need to find the shortest path between routers, and so
on.
Breadth First search is one of the shortest path-finding algorithms
and it helps us identify the shortest path between two vertices.
We are going to apply this algorithm to the `persons` dataset to
find the shortest path for Bob to connect to William. You can see that
only Andrew is connected to William. So, for Bob to be able to connect
to William, what is the shortest path? This is what we are going to
determine using the following code.

How It Works

GraphFrames provides an API, called `bfs`, which takes a
minimum of two parameters. Those are:

*   `fromExpr`: Expression
    to identify the `from` vertex.

*   `toExpr`:
    Expression to identify the `to` vertex.

Yes, it can be expressions, which means you can do it for multiple
vertices. First, we will see it for one vertex. After that, we will
apply it to multiple vertices.

```
>>> graph.bfs(
...   fromExpr = "name = 'Bob'",
...   toExpr = "name = 'William'",
...   ).show()
```

From this code notice that we are calling the `bfs` method with two
inputs—`fromExpr`
and `toExpr`—and
with filters called `= 'Bob'` and `"name = 'William'"`. This is to say that we are looking for the shortest
path between Bob and William.

Here is the output:

```
+------------+-----------+---------------+--------------+----------------+
|        from|         e0|             v1|            e1|              to|
+------------+-----------+---------------+--------------+----------------+
|[3, Bob, 12]|[3, 1, Son]|[1, Andrew, 45]|[1, 5, Friend]|[5, William, 35]|
+------------+-----------+---------------+--------------+----------------+
```

From the previous output, you can infer that for Bob to connect to
William, he needs to go through Andrew.

Let’s try to apply more than one vertex to this. We will try to find all
the people younger than 20 to be able to connect to Rachel.

```
>>> graph.bfs(
...   fromExpr = "age < 20",
...   toExpr = "name = 'Rachel'",
...   ).show()
```

In the previous code snippet, we modified the expressions so that we
are looking for all people younger than 20 to find ways to connect to
Rachel.

Here is the output:

```
+--------------+----------------+---------------+--------------+---------------+
|          from|              e0|             v1|            e1|             to|
+--------------+----------------+---------------+--------------+---------------+
|  [3, Bob, 12]|     [3, 1, Son]|[1, Andrew, 45]|[1, 6, Friend]|[6, Rachel, 32]|
|  [3, Bob, 12]|     [3, 2, Son]|[2, Sierra, 43]|[2, 6, Friend]|[6, Rachel, 32]|
|[4, Emily, 10]|[4, 1, Daughter]|[1, Andrew, 45]|[1, 6, Friend]|[6, Rachel, 32]|
|[4, Emily, 10]|[4, 2, Daughter]|[2, Sierra, 43]|[2, 6, Friend]|[6, Rachel, 32]|
+--------------+----------------+---------------+--------------+---------------+
```

Notice that Bob and Emily are both listed in the output. Since Rachel
is a friend to both Andrew and Sierra, they are the vertex between Bob
and Emily. Bob and Emily need to go through either Andrew or Sierra to
be able to connect to Rachel.
If you want to restrict some of the paths—let’s say you want the kids
to only go through the parents—then you can use the `edgeFilter` to
determine through which relationships Bob and Emily can connect to
Rachel.
The following code shows the usage of the `edgeFilter` attribute.
We are going to say that only the daughter is allowed in the
results.

Here is the code that uses the `edgeFilter` attribute
to filter out the edges while identifying the shortest paths:

```
>>> graph.bfs(
...   fromExpr = "age < 20",
...   toExpr = "name = 'Rachel'",
...   edgeFilter = "relation != 'Son'"
...   ).show()
```

Here is the output that shows how Emily can connect to Rachel:

```
+--------------+----------------+---------------+--------------+---------------+
|          from|              e0|             v1|            e1|             to|
+--------------+----------------+---------------+--------------+---------------+
|[4, Emily, 10]|[4, 1, Daughter]|[1, Andrew, 45]|[1, 6, Friend]|[6, Rachel, 32]|
|[4, Emily, 10]|[4, 2, Daughter]|[2, Sierra, 43]|[2, 6, Friend]|[6, Rachel, 32]|
+--------------+----------------+---------------+--------------+---------------+
```

With this, you have successfully learned about the Breadth First
search algorithm and used it for analysis.

Index

A

adduser command

allBigData

Apache Hadoop

Apache Hive

code execution flow

HiveQL commands

RDBMS

SQL

Apache Kafka

broker

consumer

development

message flow

producer

Apache Mesos cluster manager

Apache Pig

Apache Spark

description

GraphFrames

MLlib

Resilient Distributed Datasets

Apache Storm

Apache Tez

Atomicity, Consistency, Isolation and Durability (ACID) principles

B

Big Data

Apache Hadoop

Apache Storm

Apache Tez

variety

velocity

veracity

volume

Big Data frameworks

Breadth first algorithm

API

path-finding algorithms

C

Cassandra

Cassandra installation

Catalyst optimizer

Cluster By clause

Cluster managers

Apache Mesos

distributed system

standalone

YARN

Comma-separated value (CSV) file

DataFrame

header argument

inferSchema argument

reading

spark.read.csv() function

swimmerData.csv

Console sink

Contingency table

creation

MongoDB command

restaurant survey

Correlation

Covariance

Cross tabulations

CSV File, DataFrame creation

JSON format

parquet format

sample data

D, E

Data aggregation

multiple key

department columns

gender columns

multiple columns

single key

average frequency

data from MySQL

functions

gender-wise

mean value

DataFrames

categorical variables

correlation

covariance

CSV file

describe() function

frequent items

horizontal stacking

inner join

new creation

PostgreSQL

joining

arguments

Cassandra table

full outer joins

inner joins

left outer joins

right outer joins

students table

subjects table

types of joins

JSON file

MongoDB

MySQL

ORC file

Parquet file

PostgreSQL

removing duplicate records

sample records ( *see* Sampling data)

simple SQL creation

create aliases

filtering on case-sensitive

use alias columns

using column names

where clause filtering

summary() function

swimming competition ( *see* Swimming competition, dataframes)

temp view creation

vertical stacking

new creation

PostgreSQL

SQL commands

DataFrame streaming

creation

join static data

output modes

PySparkSQL queries

set up

sink types

SQL filters

static data

temperature data

Data labeling

Degrees

Descriptive statistics

agg() function

corrData.json

counting, number of elements

population variance

pyspark.sql.functions submodule

sample mean

sample variance

spark.read.json function

summation, mean, and standard deviation

variance, mean, and standard deviation

Directed Acyclic Graphs

Distribute By clause

Distributed systems

drop() function

dropna() function

F

File sink

File source

fillna() function

G

Google File System (GFS)

GraphFrames

creation

DataFrames

degrees

error message

installation

persons.csv

personsDf DataFrame

relationship.csv

triangle count value

vertices

groupby() function

H, I

Hadoop

components

The Google File System

MapReduce

Hadoop Distributed File System (HDFS)

Hadoop ecosystem frameworks

Hadoop installation, single machine

.bashrc file updation

CentOS user creation

directory installation

downloading

environment file

HDFS

java installation

jps command checking

namenode format running

password-less login creation

properties files

start-dfs.sh script

start-yarn.sh script

stop-dfs.sh shell script

YARN

Hive installation, single machine

.bashrc file

datawarehouse directory creation

directory

downloading

extraction

hive-site.xml

metastore database

Hive metastore configuration, PostgreSQL

downloading JDBC connector

external RDBMS

grant command

hive-site.xml modification

lib directory

pg_hba.conf file

pymetastore database

testing

user and database creation

Horizontal scaling

J

JavaScript Object Notation (JSON) file

corrData.json

DoubleType()

spark.read.json() function

Jobtracker

join() function

K, L

Kafka sink

Kafka source

M, N

MapReduce

Hadoop streaming module

HDFS

iterative algorithms

Jobtracker and Tasktracker

MapReduce framework

maxIter

Missing value imputation

arguments

drop the rows

MongoDB

replace with zero

thresh argument

MongoDB

MySQL

MySQL server installation

O

Optimized Row Columnar (ORC) file

P

PageRank algorithm

Apache Spark

attributes

Parquet file

Partition-wise sorting

Occupation and swimTimeInSecond columns

process of

repartitioning and shuffling

single

sortWithinPartitions() function

passwd command

PostgreSQL

printSchema() function

Procedural Language/PostgreSQL (PL/pgSQL)

PySpark shell

PySpark SQL

aggregation

Group By clause

number of students per subject

number of subjects per student

cache data

catalyst optimizer

Cluster By clause

DataFrames

Distribute By and Sort By clause

domain-specific language

file format systems

SparkSession

Structured Streaming

windows functions

partition

rank function

ranking in place

PySpark to Hive, connection

PySpark UDF creation

genderCodeToValue

Python function

Q

Query Optimizer

R

rank() function

Rate source

Relational Database Management Systems (RDBMSs)

Reset probability

Resilient Distributed Dataset (RDD)

restaurantSurvey

S

Sampling data

noDuplicateDf1 DataFrame

column iv1

without replacement

with replacement

sampleBy(col, fractions, seed=None)

sample(withReplacement, fraction, seed=None)

time-intensive and computation-intensive

Shuffling in Spark

Socket source

Sort By clause

Sorting

ascending order

descending order

Occupation and swimTimeInSecond columns

PySpark SQL API orderBy(*cols, **kwargs)

partition-wise ( *see* Partition-wise sorting)

swimmerDf

Spark installation, single machine

.bashrc file

directory/allBigData

downloading

environment file changing

pyspark script

.tgz file extraction

SparkSession

Spark SQLs

joining multiple DataFrames

applying queries

dataset

joining two DataFrames

boilerplate code

dataset

join query specifics

observations

running the queries

using full outer join

using left join

using right outer join

UDF methods

date functions

dateofbirth column

Spark terminology

Standalone cluster manager

Stream computation

Structured Streaming

Swimming competition, dataframes

column deletion

swimmerDf2

structured datasets

column selection

id and swimmerSpeed columns

select() function

swimmerDf2 DataFrame

swimTimeInSecond column

data labeling

filtering process

gender column

occupation and swimmerSpeed > 1.17

where() function

sort data ( *see* Sorting)

transformation operation

CSV file

printSchema() function

round() function

sample data

swimming speed

withColumn() function

UDF ( *see* User-defined function (UDF))

T

Tasktracker

Temperature dataset

Triangle count value

U

union() function

User Defined Aggregate Functions (UDAF)

User-defined function (UDF)

average temperature

celsiustoFahrenheit

CSV file

Fahrenheit column

pyspark.sql.functions

spark.read.parquet() function

tempDfFahrenheit

temperatureData

temperature value, Celsius and Fahrenheit

tempInCelsius

V

Vertical scaling

Vertical stacking

W, X

writeStream method

Y, Z

Yet Another Resource Negotiator (YARN) cluster manager

```