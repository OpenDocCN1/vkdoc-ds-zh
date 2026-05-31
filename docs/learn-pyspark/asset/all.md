![](images/978-1-4842-4961-1_CoverFigure.jpg)

ISBN 978-1-4842-4960-4e-ISBN 978-1-4842-4961-1 [https://doi.org/10.1007/978-1-4842-4961-1](https://doi.org/10.1007/978-1-4842-4961-1) © Pramod Singh 2019 Apress Standard Trademarked names, logos, and images may appear in this book. Rather than use a trademark symbol with every occurrence of a trademarked name, logo, or image, we use the names, logos, and images only in an editorial fashion and to the benefit of the trademark owner, with no intention of infringement of the trademark. The use in this publication of trade names, trademarks, service marks, and similar terms, even if they are not identified as such, is not to be taken as an expression of opinion as to whether or not they are subject to proprietary rights. While the advice and information in this book are believed to be true and accurate at the date of publication, neither the author nor the editors nor the publisher can accept any legal responsibility for any errors or omissions that may be made. The publisher makes no warranty, express or implied, with respect to the material contained herein. Distributed to the book trade worldwide by Springer Science+Business Media New York, 233 Spring Street, 6th Floor, New York, NY 10013\. Phone 1-800-SPRINGER, fax (201) 348-4505, e-mail orders-ny@springer-sbm.com, or visit www.springeronline.com. Apress Media, LLC is a California LLC and the sole member (owner) is Springer Science+Business Media Finance Inc (SSBM Finance Inc). SSBM Finance Inc is a Delaware corporation.

*I dedicate this book to my wife, Neha, my son, Ziaan, and my parents. Without you, this book wouldn’t have been possible. You complete my world and are the source of my strength.*

Introduction

The idea of writing this book had already been seeded while I was working on my first book, and there was a strong reason for that. The earlier book was more focused on machine learning using big data and essentially did not deep-dive sufficiently into supporting aspects, but this book goes a little deeper into the internals of Spark’s machine learning library, as well as analyzing of streaming data. It is a good reference point for someone who wants to learn more about how to automate different workflows and build pipelines to handle real-time data.

This book is divided into three main sections. The first provides an introduction to Spark and data analysis on big data; the middle section discusses using Airflow for executing different jobs, in addition to data analysis on streaming data, using the structured streaming features of Spark. The final section covers translation of a business problem into machine learning and solving it, using Spark’s machine learning library, with a deep dive into deep learning as well.

This book might also be useful to data analysts and data engineers, as it covers the steps of big data processing using PySpark. Readers who want to make a transition to the data science and machine learning fields will also find this book a good starting point and can gradually tackle more complicated areas later. The case studies and examples given in the book make it really easy to follow and understand the related fundamental concepts. Moreover, there are very few books available on PySpark, and this book certainly adds value to readers’ knowledge. The strength of this book lies in its simplicity and on its application of machine learning to meaningful datasets.

I have tried my best to put all my experience and knowledge into this book, and I feel it is particularly relevant to what businesses are seeking in order to solve real challenges. I hope that it will provide you with some useful takeaways.

Acknowledgments

This is my second book on Spark, and along the way, I have come to realize my love for handling big data and performing machine learning as well. Going forward, I intend to write many more books, but first, let me thank a few people who have helped me along this journey. First, I must thank the most important person in my life, my beloved wife, Neha, who selflessly supported me throughout and sacrificed so much to ensure that I completed this book.

I must thank Celestin Suresh John, who believed in me and extended the opportunity to write this book. Aditee Mirashi is one of the best editors in India. This is my second book with her, and it was even more exciting to work with her this time. As usual, she was extremely supportive and always there to accommodate my requests. I especially would like to thank Jim Markham, who dedicated his time to reading every single chapter and offered so many useful suggestions. Thanks, Jim, I really appreciate your input. I also want to thank Manoj Patil, who had the patience to review every line of code and check the appropriateness of each example. Thank you for your feedback and encouragement. It really made a difference to me and the book.

I also want to thank the many mentors who have constantly forced me to pursue my dreams. Thank you Sebastian Keupers, Dr. Vijay Agneeswaran, Sreenivas Venkatraman, Shoaib Ahmed, and Abhishek Kumar, for your time. Finally, I am infinitely grateful to my son, Ziaan, and my parents, for their endless love and support, irrespective of circumstances. You all make my world beautiful.

### About the Author and About the Technical Reviewer

### About the Author

### About the Technical Reviewer

# 1. Introduction to Spark

As this book is about Spark, it makes perfect sense to start the first chapter by looking into some of Spark’s history and its different components. This introductory chapter is divided into three sections. In the first, I go over the evolution of data and how it got as far as it has, in terms of size. I’ll touch on three key aspects of data. In the second section, I delve into the internals of Spark and go over the details of its different components, including its architecture and modus operandi. The third and final section of this chapter focuses on how to use Spark in a cloud environment.

## History

The birth of the Spark project occurred at the Algorithms, Machine, and People (AMP) Lab at the University of California, Berkeley. The project was initiated to address the potential issues in the Hadoop MapReduce framework. Although Hadoop MapReduce was a groundbreaking framework to handle big data processing, in reality, it still had a lot of limitations in terms of speed. Spark was new and capable of doing in-memory computations, which made it almost 100 times faster than any other big data processing framework. Since then, there has been a continuous increase in adoption of Spark across the globe for big data applications. But before jumping into the specifics of Spark, let’s consider a few aspects of data itself.

Data can be viewed from three different angles: the way it is collected, stored, and processed, as shown in Figure [1-1](#478390_1_En_1_Chapter.xhtml#Fig1).

![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig1_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig1_HTML.png)

Figure 1-1

Three aspects of data

### Data Collection

A huge shift in the manner in which data is collected has occurred over the last few years. From buying an apple at a grocery store to deleting an app on your mobile phone, every data point is now captured in the back end and collected through various built-in applications. Different Internet of things (IoT) devices capture a wide range of visual and sensory signals every millisecond. It has become relatively convenient for businesses to collect that data from various sources and use it later for improved decision making.

### Data Storage

In previous years, no one ever imagined that data would reside at some remote location, or that the cost to store data would be as cheap as it is. Businesses have embraced cloud storage and started to see its benefits over on-premise approaches. However, some businesses still opt for on-premise storage, for various reasons. It’s known that data storage began by making use of magnetic tapes. Then the breakthrough introduction of floppy discs made it possible to move data from one place to another. However, the size of the data was still a huge limitation. Flash drives and hard discs made it even easier to store and transfer large amounts of data at a reduced cost. (See Figure [1-2](#478390_1_En_1_Chapter.xhtml#Fig2).) The latest trend in the advancement of storage devices has resulted in flash drives capable of storing data up to 2TBs, at a throwaway price.

![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig2_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig2_HTML.png)

Figure 1-2

Evolution of data storage

This trend clearly indicates that the cost to store data has been reduced significantly over the years and continues to decline. As a result, businesses don’t shy away from storing huge amounts of data, irrespective of its kind. From logs to financial and operational transactions to simple employee feedback, everything gets stored.

### Data Processing

The final aspect of data is using stored data and processing it for some analysis or to run an application. We have witnessed how efficient computers have become in the last 20 years. What used to take five minutes to execute probably takes less than a second using today’s machines with advanced processing units. Hence, it goes without saying that machines can process data much faster and easier. Nonetheless, there is still a limit to the amount of data a single machine can process, regardless of its processing power. So, the underlying idea behind Spark is to use a collection (cluster) of machines and a unified processing engine (Spark) to process and handle huge amounts of data, without compromising on speed and security. This was the ultimate goal that resulted in the birth of Spark.

## Spark Architecture

There are five core components that make Spark so powerful and easy to use. The core architecture of Spark consists of the following layers, as shown in Figure [1-3](#478390_1_En_1_Chapter.xhtml#Fig3):

![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig3_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig3_HTML.png)

Figure 1-3

Core components of Spark

*   Storage

*   Resource management

*   Engine

*   Ecosystem

*   APIs

### Storage

Before using Spark, data must be made available in order to process it. This data can reside in any kind of database. Spark offers multiple options to use different categories of data sources, to be able to process it on a large scale. Spark allows you to use traditional relational databases as well as NoSQL, such as Cassandra and MongoDB.

### Resource Management

The next layer consists of a resource manager. As Spark works on a set of machines (it also can work on a single machine with multiple cores), it is known as a Spark *cluster* . Typically, there is a resource manager in any cluster that efficiently handles the workload between these resources. The two most widely used resource managers are YARN and Mesos. The resource manager has two main components internally:

1.  *Cluster manager*

2.  *Worker*

It’s kind of like master-slave architecture, in which the cluster manager acts as a master node, and the worker acts as a slave node in the cluster. The cluster manager keeps track of all information pertaining to the worker nodes and their current status. Cluster managers always maintain the following information:

*   Status of worker node (busy/available)

*   Location of worker node

*   Memory of worker node

*   Total CPU cores of worker node

The main role of the cluster manager is to manage the worker nodes and assign them tasks, based on the availability and capacity of the worker node. On the other hand, a worker node is only responsible for executing the task it’s given by the cluster manager, as shown in Figure [1-4](#478390_1_En_1_Chapter.xhtml#Fig4).

![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig4_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig4_HTML.png)

Figure 1-4

Resource management

The tasks that are given to the worker nodes are generally the individual pieces of the overall Spark application. The Spark application contains two parts:

1.  Task

2.  Spark driver

The task is the data processing logic that has been written in either PySpark or Spark R code. It can be as simple as taking a total frequency count of words to a very complex set of instructions on an unstructured dataset. The second component is Spark driver, the main controller of a Spark application, which consistently interacts with a cluster manager to find out which worker nodes can be used to execute the request. The role of the Spark driver is to request the cluster manager to initiate the Spark executor for every worker node.

### Engine and Ecosystem

The base of the Spark architecture is its core, which is built on top of RDDs (Resilient Distributed Datasets) and offers multiple APIs for building other libraries and ecosystems by Spark contributors. It contains two parts: the distributed computing infrastructure and the RDD programming abstraction. The default libraries in the Spark toolkit come as four different offerings.

#### Spark SQL

SQL being used by most of the ETL operators across the globe makes it a logical choice to be part of Spark offerings. It allows Spark users to perform structured data processing by running SQL queries. In actuality, Spark SQL leverages the catalyst optimizer to perform the optimizations during the execution of SQL queries.

Another advantage of using Spark SQL is that it can easily deal with multiple database files and storage systems such as SQL, NoSQL, Parquet, etc.

#### MLlib

Training machine learning models on big datasets was starting to become a huge challenge, until Spark’s MLlib (Machine Learning library) came into existence. MLlib gives you the ability to train machine learning models on huge datasets, using Spark clusters. It allows you to build in supervised, unsupervised, and recommender systems; NLP-based models; and deep learning, as well as within the Spark ML library.

#### Structured Streaming

The Spark Streaming library provides the functionality to read and process real-time streaming data. The incoming data can be batch data or near real-time data from different sources. Structured Streaming is capable of ingesting real-time data from such sources as Flume, Kafka, Twitter, etc. There is a dedicated chapter on this component later in this book (see Chapter [3](#478390_1_En_3_Chapter.xhtml)).

#### Graph X

This is a library that sits on top of the Spark core and allows users to process specific types of data (graph dataframes), which consists of nodes and edges. A typical graph is used to model the relationship between the different objects involved. The nodes represent the object, and the edge between the nodes represents the relationship between them. Graph dataframes are mainly used in network analysis, and Graph X makes it possible to have distributed processing of such graph dataframes.

### Programming Language APIs

Spark is available in four languages. Because Spark is built using Scala, that becomes the native language. Apart from Scala, we can also use Python, Java, and R, as shown in Figure [1-5](#478390_1_En_1_Chapter.xhtml#Fig5).

![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig5_HTML.jpg](images/478390_1_En_1_Chapter/478390_1_En_1_Fig5_HTML.jpg)

Figure 1-5

Language APIs

## Setting Up Your Environment

In this final section of this chapter, I will go over how to set up the Spark environment in the cloud. There are multiple ways in which we can use Spark:

*   Local setup

*   Dockers

*   Cloud environment (GCP, AWS, Azure)

*   Databricks

### Local Setup

It is relatively easy to install and use Spark on a local system, but it fails the core purpose of Spark itself, if it’s not used on a cluster. Spark’s core offering is distributed data processing, which will always be limited to a local system’s capacity, in the case that it’s run on a local system, whereas one can benefit more by using Spark on a group of machines instead. However, it is always good practice to have Spark locally, as well as to test code on sample data. So, follow these steps to do so:

1.  Ensure that Java is installed; otherwise install Java.

2.  Download the latest version of Apache Spark from [`https://spark.apache.org/downloads.html`](https://spark.apache.org/downloads.html) .

3.  Extract the files from the zipped folder.

4.  Copy all the Spark-related files to their respective directory.

5.  Configure the environment variables to be able to run Spark.

6.  Verify the installation and run Spark.

### Dockers

Another way of using Spark locally is through the containerization technique of dockers . This allows users to wrap all the dependencies and Spark files into a single image, which can be run on any system. We can kill the container after the task is finished and rerun it, if required. To use dockers for running Spark, we must install Docker on the system first and then simply run the following command: `[In]: docker run -it -p 8888:8888 jupyter/pyspark-notebook"`.

### Cloud Environments

As discussed earlier in this chapter, for various reasons, local sets are not of much help when it comes to big data, and that’s where cloud-based environments make it possible to ingest and process huge datasets in a short period. The real power of Spark can be seen easily while dealing with large datasets (in excess of 100TB). Most of the cloud-based infra-providers allow you to install Spark, which sometimes comes preconfigured as well. One can easily spin up the clusters with required specifications, according to need. One of the cloud-based environments is Databricks.

#### Databricks

Databricks is a company founded by the creators of Spark, in order to provide the enterprise version of Spark to businesses, in addition to full-fledged support. To increase Spark’s adoption among the community and other users, Databricks also provides a free community edition of Spark, with a 6GB cluster (single node). You can increase the size of the cluster by signing up for an enterprise account with Databricks, using the following steps:

1.  Search for the Databricks web site and select Databricks Community Edition, as shown in Figure [1-6](#478390_1_En_1_Chapter.xhtml#Fig6).

    ![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig6_HTML.jpg](images/478390_1_En_1_Chapter/478390_1_En_1_Fig6_HTML.jpg)

    Figure 1-6

    Databricks web page

2.  If you have a user account with Databricks, you can simply log in. If you don’t have an account, you must create one, in order to use Databricks, as shown in Figure [1-7](#478390_1_En_1_Chapter.xhtml#Fig7).

    ![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig7_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig7_HTML.png)

    Figure 1-7

    Databricks login

3.  Once you are on the home page, you can choose to either load a new data source or create a notebook from scratch, as shown in Figure [1-8](#478390_1_En_1_Chapter.xhtml#Fig8). In the latter case, you must have the cluster up and running, to be able to use the notebook. Therefore, you must click New Cluster, to spin up the cluster. (Databricks provides a 6GB AWS EMR cluster.)

    ![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig8_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig8_HTML.png)

    Figure 1-8

    Creating a Databricks notebook

4.  To set up the cluster, you must give a name to the cluster and select the version of Spark that must configure with the Python version, as shown in Figure [1-9](#478390_1_En_1_Chapter.xhtml#Fig9). Once all the details are filled in, you must click Create Cluster and wait a couple of minutes, until it spins up.

    ![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig9_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig9_HTML.png)

    Figure 1-9

    Creating a Databricks cluster

5.  You can also view the status of the cluster by going into the Clusters option on the left side widget, as shown in Figure [1-10](#478390_1_En_1_Chapter.xhtml#Fig10). It gives all the information associated with the particular cluster and its current status.

    ![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig10_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig10_HTML.png)

    Figure 1-10

    Databricks cluster list

6.  The final step is to open a notebook and attach it to the cluster you just created (Figure [1-11](#478390_1_En_1_Chapter.xhtml#Fig11)). Once attached, you can start the PySpark code.

    ![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig11_HTML.png](images/478390_1_En_1_Chapter/478390_1_En_1_Fig11_HTML.png)

    Figure 1-11

    Databricks notebook

Overall, since 2010, when Spark became an open source platform, its users have risen in number consistently, and the community continues to grow every day. It’s no surprise that the number of contributors to Spark has outpaced that of Hadoop. Some of the reasons for Spark’s popularity were noted in a survey, the results of which are shown in Figure [1-12](#478390_1_En_1_Chapter.xhtml#Fig12).

![../images/478390_1_En_1_Chapter/478390_1_En_1_Fig12_HTML.jpg](images/478390_1_En_1_Chapter/478390_1_En_1_Fig12_HTML.jpg)

Figure 1-12

Results of Spark adoption survey

## Conclusion

This chapter provided a brief history of Spark, its core components, and the process of accessing it in a cloud environment. In upcoming chapters, I will delve deeper into the various aspects of Spark and how to build different applications with it.

# 2. Data Processing

This chapter covers different steps to preprocess and handle data in PySpark. Preprocessing techniques can certainly vary from case to case, and many different methods can be used to massage the data into desired form. The idea of this chapter is to expose some of the common techniques for dealing with big data in Spark. In this chapter, we are going to go over different steps involved in preprocessing data, such as handling missing values, merging datasets, applying functions, aggregations, and sorting. One major part of data preprocessing is the transformation of numerical columns into categorical ones and vice versa, which we are going to look at over the next few chapters and are based on machine learning. The dataset that we are going to make use of in this chapter is inspired by a primary research dataset and contains a few attributes from the original dataset, with additional columns containing fabricated data points.

### Note

All the following steps are written in Jupyter Notebook, running Spark on a Docker image (mentioned in Chapter [1](#478390_1_En_1_Chapter.xhtml)). All the subsequent code can also be run in Databricks.

## Creating a SparkSession Object

The first step is to create a `SparkSession` object , in order to use Spark. We also import all the required functions and datatypes from `spark.sql`:

```
[In]: from pyspark.sql import SparkSession
[In]: spark=SparkSession.builder.appName('data_processing').getOrCreate()
[In]: import pyspark.sql.functions as F
[In]: from pyspark.sql.types import *
```

Now, instead of directly reading a file to create a dataframe, we go over the process of creating a dataframe, by passing key values. The way we create a dataframe in Spark is by declaring its schema and pass the columns values.

### Creating Dataframes

In the following example, we are creating a new dataframe with five columns of certain datatypes (string and integer). As you can see, when we call show on the new dataframe, it is created with three rows and five columns containing the values passed by us.

```
[In]:schema=StructType().add("user_id","string").add("country","string").add("browser", "string").add("OS",'string').add("age", "integer")
[In]: df=spark.createDataFrame([("A203",'India',"Chrome","WIN",33),("A201",'China',"Safari","MacOS",35),("A205",'UK',"Mozilla","Linux",25)],schema=schema)
[In]: df.printSchema()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figa_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figa_HTML.jpg)

```
[In]: df.show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figb_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figb_HTML.jpg)

### Null Values

It is very common to have null values as part of the overall data. Therefore, it becomes critical to add a step to the data processing pipeline, to handle the null values. In Spark, we can deal with null values by either replacing them with some specific value or dropping the rows/columns containing null values.

First, we create a new dataframe (`df_na`) that contains null values in two of its columns (the schema is the same as in the earlier dataframe). By the first approach to deal with null values, we fill all null values in the present dataframe with a value of 0, which offers a quick fix. We use the `fillna` function to replace all the null values in the dataframe with 0\. By the second approach, we replace the null values in specific columns (country, browser) with `'USA'` and `'Safari'`, respectively.

```
[In]: df_na=spark.createDataFrame([("A203",None,"Chrome","WIN",33),("A201",'China',None,"MacOS",35),("A205",'UK',"Mozilla","Linux",25)],schema=schema)
[In]: df_na.show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figc_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figc_HTML.jpg)

```
[In]: df_na.fillna('0').show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figd_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figd_HTML.jpg)

```
[In]: df_na.fillna( { 'country':'USA', 'browser':'Safari' } ).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Fige_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Fige_HTML.jpg)

In order to drop the rows with any null values, we can simply use the `na.drop` functionality in PySpark. Whereas if this needs to be done for specific columns, we can pass the set of column names as well, as shown in the following example:

```
[In]: df_na.na.drop().show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figf_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figf_HTML.jpg)

```
[In]: df_na.na.drop(subset='country').show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figg_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figg_HTML.jpg)

Another very common step in data processing is to replace some data points with particular values. We can use the `replace` function for this, as shown in the following example. To drop the column of a dataframe, we can use the `drop` functionality of PySpark.

```
[In]: df_na.replace("Chrome","Google Chrome").show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figh_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figh_HTML.jpg)

```
[In]: df_na.drop('user_id').show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figi_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figi_HTML.jpg)

Now that we have seen how to create a dataframe by passing a value and how to treat missing values, we can create a Spark dataframe, by reading a file (`.csv`, `parquet`, etc.). The dataset contains a total of seven columns and 2,000 rows. The summary function allows us to see the statistical measures of the dataset, such as the min, max, and mean of the numerical data present in the dataframe.

```
[In]: df=spark.read.csv("customer_data.csv",header=True,inferSchema=True)
[In]: df.count()
[Out]: 2000
[In]: len(df.columns)
[Out]: 7
[In]: df.printSchema()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figj_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figj_HTML.jpg)

```
[In]: df.show(3)
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figk_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figk_HTML.jpg)

```
[In]: df.summary().show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figl_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figl_HTML.jpg)

Most of the time, we won’t use all the columns present in the dataframe, as some might be redundant and carry very little value in terms of providing useful information. Therefore, subsetting the dataframe becomes critical for having proper data in place for analysis. I’ll cover this in the next section.

## Subset of a Dataframe

A subset of a dataframe can be created, based on multiple conditions in which we either select a few rows, columns, or data with certain filters in place. In the following examples, you will see how we can create a subset of the original dataframe, based on certain conditions, to demonstrate the process of filtering records.

*   Select

*   Filter

*   Where

### Select

In this example, we take one of the dataframe columns, `'Avg_Salary'`, and create a subset of the original dataframe, using `select`. We can pass any number of columns that must be present in the subset. We then apply a filter on the dataframe, to extract the records, based on a certain threshold (`Avg_Salary > 1000000`). Once filtered, we can either take the total count of records present in the subset or take it for further processing.

```
[In]: df.select(['Customer_subtype','Avg_Salary']).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figm_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figm_HTML.jpg)

```
[In]: df.filter(df['Avg_Salary'] > 1000000).count()
[In]: 128
[In]: df.filter(df['Avg_Salary'] > 1000000).show()
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Fign_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Fign_HTML.jpg)

### Filter

We can also apply more than one filter on the dataframe, by including more conditions, as shown following. This can be done in two ways: first, by applying consecutive filters, then by using `(&`, `or)` operands with a `where` statement.

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figo_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figo_HTML.jpg)

```
[In]: df.filter(df['Avg_Salary'] > 500000).filter(df['Number_of_houses'] > 2).show()
[Out]:
```

### Where

```
[In]: df.where((df['Avg_Salary'] > 500000) & (df['Number_of_houses'] > 2)).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figp_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figp_HTML.jpg)

Now that we have seen how to create a subset from a dataframe, we can move on to aggregations in PySpark.

## Aggregations

Any kind of aggregation can be broken simply into three stages, in the following order:

*   Split

*   Apply

*   Combine

The first step is to split the data, based on a column or group of columns, followed by performing the operation on those small individual groups (count, max, avg, etc.). Once the results are in for each set of groups, the last step is to combine all these results.

In the following example, we aggregate the data, based on `'Customer subtype'`, and simply count the number of records in each category. We use the `groupBy` function in PySpark. The output of this is not in any particular order, as we have not applied any sorting to the results. Therefore, we will also see how we can apply any type of sorting to the final results. Because we have seven columns in the dataframe—all are categorical columns except for one `(Avg_Salary)`, we can iterate over each column and apply aggregation as in the following example:

```
[In]: df.groupBy('Customer_subtype').count().show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figq_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figq_HTML.jpg)

```
[In]:
for col in df.columns:
if col !='Avg_Salary':
print(f" Aggregation for  {col}")
df.groupBy(col).count().orderBy('count',ascending=False).show(truncate=False)
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figr_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figr_HTML.jpg)

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figs_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figs_HTML.jpg)

As mentioned, we can have different kinds of operations on groups of records, such as

*   Mean

*   Max

*   Min

*   Sum

The following examples cover some of these, based on different groupings. `F` refers to the Spark `sql` function here.

```
[In]: df.groupBy('Customer_main_type').agg(F.mean('Avg_Salary')).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figt_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figt_HTML.jpg)

```
[In]: df.groupBy('Customer_main_type').agg(F.max('Avg_Salary')).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figu_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figu_HTML.jpg)

```
[In]: df.groupBy('Customer_main_type').agg(F.min('Avg_Salary')).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figv_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figv_HTML.jpg)

```
[In]: df.groupBy('Customer_main_type').agg(F.sum('Avg_Salary')).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figw_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figw_HTML.jpg)

Sometimes, there is simply a need to sort the data with aggregation or without any sort of aggregation. That’s where we can make use of the `'sort` `'` and `'orderBy'` functionality of PySpark, to rearrange data in a particular order, as shown in the following examples:

```
[In]: df.sort("Avg_Salary", ascending=False).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figx_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figx_HTML.jpg)

```
[In]: df.groupBy('Customer_subtype').agg(F.avg('Avg_Salary').alias('mean_salary')).orderBy('mean_salary',ascending=False).show(50,False)
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figy_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figy_HTML.jpg)

```
[In]: df.groupBy('Customer_subtype').agg(F.max('Avg_Salary').alias('max_salary')).orderBy('max_salary',ascending=False).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figz_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figz_HTML.jpg)

In some cases, we must also collect the list of values for particular groups or for individual categories. For example, let’s say a customer goes to an online store and accesses different pages on the store’s web site. If we have to collect all the customer’s activities in a list, we can use the `collect` functionality in PySpark. We can collect values in two different ways:

*   Collect List

*   Collect Set

### Collect

Collect list provides all the values in the original order of occurrence (they can be reversed as well), and collect set provides only the unique values, as shown in the following example. We consider grouping on `Customer subtype` and collecting the `Numberof houses` values in a new column, using `list` and `set` separately.

```
[In]: df.groupby("Customer_subtype").agg(F.collect_set("Number_of_houses")).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figaa_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figaa_HTML.jpg)

```
[In]:
df.groupby("Customer_subtype").agg(F.collect_list("Number_of_houses")).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figab_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figab_HTML.jpg)

The need to create a new column with a constant value can be very common. Therefore, we can do that in PySpark, using the `'lit'` function. In the following example, we create a new column with a constant value:

```
[In]: df=df.withColumn('constant',F.lit('finance'))
[In]: df.select('Customer_subtype','constant').show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figac_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figac_HTML.jpg)

Because we are dealing with dataframes, it is a common requirement to apply certain custom functions on specific columns and get the output. Hence, we make use of UDFs, in order to apply Python functions on one or more columns.

## User-Defined Functions (UDFs)

In this example, we are trying to name the age categories and create a standard Python function (`age_category`) for the same. In order to apply this on the Spark dataframe, we create a UDF object, using this Python function. The only requirement is to mention the return type of the function. In this case, it is simply a string value.

```
[In]: from pyspark.sql.functions import udf
[In]: df.groupby("Avg_age").count().show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figad_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figad_HTML.jpg)

```
[In]: def age_category(age):
if age  == "20-30 years":
return "Young"
elif age== "30-40 years":
return "Mid Aged"
elif ((age== "40-50 years") or (age== "50-60 years")) :
return "Old"
else:
return "Very Old"
[In]: age_udf=udf(age_category,StringType())
[In]: df=df.withColumn('age_category',age_udf(df['Avg_age']))
[In]: df.select('Avg_age','age_category').show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figae_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figae_HTML.jpg)

```
[In]: df.groupby("age_category").count().show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figaf_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figaf_HTML.jpg)

Pandas UDFs are another recent advancement, so let’s review them now.

### Pandas UDF

Pandas UDFs are much faster and efficient, in terms of processing and execution time, compared to standard Python UDFs. The main difference between a normal Python UDF and a Pandas UDF is that a Python UDF is executed row by row and, therefore, really doesn’t offer the advantage of a distributed framework. It can take longer, compared to a Pandas UDF, which executes block by block and gives faster results. There are three different types of Pandas UDFs: scalar, grouped map, and grouped agg. The only difference in using a Pandas UDF compared to a traditional UDF lies in the declaration. In the following example, we try to scale the `Avg_Salary` values by applying scaling. We first take the `min` and `max` values of `Avg_Salary`, subtract from each value the minimum salary from each value, and then divide by the difference between `max` and `min`.

![$$ \frac{X-{X}_{\mathrm{min}}}{X_{max}-{X}_{min}} $$](images/478390_1_En_2_Chapter/478390_1_En_2_Chapter_TeX_Equa.png)

```
[In]: df.select('Avg_Salary').summary().show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figag_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figag_HTML.jpg)

```
[In]: min_sal=1361
[In]: max_sal=48919896
[In]: from pyspark.sql.functions import pandas_udf, PandasUDFType
[In]: def scaled_salary(salary):
scaled_sal=(salary-min_sal)/(max_sal-min_sal)
return scaled_sal
[In]: scaling_udf = pandas_udf(scaled_salary, DoubleType())
[In]:df.withColumn("scaled_salary",scaling_udf(df['Avg_Salary'])).show(10,False)
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figah_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figah_HTML.jpg)

This is how we can use both conventional and Pandas UDFs to apply different conditions on the dataframe, as required.

## Joins

Merging different datasets is a very generic requirement present in most of data-processing pipelines in the big data world. PySpark offers a very convenient way to merge and pivot your dataframe values, as required. In the following example, we create a fabricated dataframe with some dummy `Region Code` values for all `Customer` types. The idea is to combine this dataframe with the original dataframe, so as to have these region codes as part of the original dataframe, as a column.

```
[In]: region_data = spark.createDataFrame([('Family with grown ups','PN'),
('Driven Growers','GJ'),
('Conservative families','DD'),
('Cruising Seniors','DL'),
('Average Family ','MN'),
('Living well','KA'),
('Successful hedonists','JH'),
('Retired and Religious','AX'),
('Career Loners','HY'),('Farmers','JH')],schema=StructType().add("Customer_main_type","string").add("Region Code","string"))
[In]: region_data.show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figai_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figai_HTML.jpg)

```
[In]: new_df=df.join(region_data,on='Customer_main_type')
[In]: new_df.groupby("Region Code").count().show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figaj_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figaj_HTML.jpg)

We took the regional count after joining the original dataframe (`df`) with the newly created `region_data` dataframe on the `Customer_main_type` column.

## Pivoting

We can use the `pivot` function in PySpark to simply create a pivot view of the dataframe for specific columns, as shown in the following example. Here, we are grouping data, based on customer type. Columns represent different age groups. The values inside the pivot table are the sum of the `Avg Salary` of each of these customer type categories for a particular age group. We also ensure that there are no nulls or empty values, by filling all nulls with 0\. In the subsequent example, we create one more pivot table, using the `label` column and take the sum of `Avg Salary` as the values inside it.

```
[In]:df.groupBy('Customer_main_type').pivot('Avg_age').sum('Avg_Salary').fillna(0).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figak_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figak_HTML.jpg)

```
[In]:df.groupBy('Customer_main_type').pivot('label').sum('Avg_Salary').fillna(0).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figal_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figal_HTML.jpg)

We split the data, based on the `Customer_main_type` column, and took the cumulative sum of the `Avg_Salary` of each of the `label` values (`0,1`), using the `pivot` function.

## Window Functions or Windowed Aggregates

This functionality in PySpark allows you to perform certain operations on groups of records known as “within the window.” It calculates the results for each row within the window. A classic example of using `window` is the various aggregations for a user during different sessions. A visitor might have multiple sessions on a particular web site and, hence `window` can be used to count the total activities of the user during each session. PySpark supports three types of `window` functions:

*   Aggregations

*   Ranking

*   Analytics

In the following example, we import the `window` function, in addition to others, such as `row_number`. The next step is to define the window. Sometimes it can be simply an ordered column, or sometimes it can be based on particular categories within a column. We will see examples of each of these. In the first example, we define the window, which is just based on the sorted `Avg Salary` column , and we rank these salaries. We create a new column `'rank'` and assign ranks to each of the `Avg Salary` values.

```
[In]: from pyspark.sql.window import Window
[In]: from pyspark.sql.functions import col,row_number
[In]: win = Window.orderBy(df['Avg_Salary'].desc())
[In]: df=df.withColumn('rank', row_number().over(win).alias('rank'))
[In]: df.show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figam_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figam_HTML.jpg)

One common requirement is to find the top-three values from a category. In this case, `window` can be used to get the results. In the following example, we define the window and partition by the `Customer subtype` column . Basically, what it does is sort the `Avg Salary` for each of the `Customer subtype` category, so now we can use `filter` to fetch the top-three salary values for each group.

```
[In]:win_1=Window.partitionBy("Customer_subtype").orderBy(df['Avg_Salary'].desc())
[In]: df=df.withColumn('rank', row_number().over(win_1).alias('rank'))
```

Now that we have a new column rank that consists of the rank or each category of `Customer`_`subtype`, we can filter the top-three ranks for each category easily.

```
[In]:
df.groupBy('rank').count().orderBy('rank').show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figan_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figan_HTML.jpg)

```
[In]: df.filter(col('rank') < 4).show()
[Out]:
```

![../images/478390_1_En_2_Chapter/478390_1_En_2_Figao_HTML.jpg](images/478390_1_En_2_Chapter/478390_1_En_2_Figao_HTML.jpg)

## Conclusion

In this chapter, I discussed different techniques to read, clean, and preprocess data in PySpark. You saw the methods to join a dataframe and create a pivot table from it. The final sections of the chapter covered UDFs and window-based operations in PySpark. The upcoming chapters will focus on handling streaming data in PySpark and machine learning using MLlib.

# 3. Spark Structured Streaming

This chapter discusses how to use Spark’s streaming API to process real-time data. The first part focuses on the main difference between streaming and batch data, in addition to their specific applications. The second section provides details on the Structured Streaming API and its various improvements over previous RDD-based Spark streaming APIs. The final section includes the code to use for Structured Streaming on incoming data and discusses how to save the output results in memory. We’ll also look at an alternative to Structured Streaming.

## Batch vs. Stream

Perhaps most readers of this book are already familiar with the key distinction between batch vs. stream data processing. Nonetheless, we can start on this note, as it emphasizes the importance of stream processing today. If we think of data as a huge ocean, then batch data can be referred to as a bucket of water, and we can have multiple buckets of different sizes, whereas stream data can be considered to be a water pipe that is continuously pumping water from the ocean.

### Batch Data

As the name suggests, *batch* refers to a group of records put together over a period of time and later used for processing and analysis. Because these records are collected over a period of time, size-wise, batch data is generally bigger than streaming data (in some cases, however, stream data can be bigger than batch data) and is often used to conduct postmortems for various analysis purposes. The legacy systems, SQL databases, and mainframes all fall under the category of batch data. The key difference compared to streaming data is that batch data is not processed as soon as it becomes part of an earlier batch dataset.

### Stream Processing

Stream processing refers to the processing of records in real time or near real time. One doesn’t wait for the day to end to then process or analyze the data. Rather, records of the dataset are processed one by one as soon as they become available or based on a window period, as shown in Figure [3-1](#478390_1_En_3_Chapter.xhtml#Fig1). As a result, this creates a sort of infinite table, with records continuously being added as data flows from the stream.

![../images/478390_1_En_3_Chapter/478390_1_En_3_Fig1_HTML.png](images/478390_1_En_3_Chapter/478390_1_En_3_Fig1_HTML.png)

Figure 3-1

Streaming data

Today, businesses are very aggressive about using real-time data from various sources, such as platforms, devices, applications, and system logs, in order to keep their competitive edge. Therefore, stream processing has become a critical part of that overall process . Businesses want to use the latest or freshest data, to generate useful insights that can help in decision making. Batch processing cannot offer analytics on the fly, as it doesn’t work on a real-time basis, whereas stream data processing can help more effectively in such cases as fraud detection.

### Spark Streaming

In the previous chapter, you have seen the core architecture of Spark. One of the components of the Spark framework is Spark Streaming (Structured Streaming), shown in Figure [3-2](#478390_1_En_3_Chapter.xhtml#Fig2).

![../images/478390_1_En_3_Chapter/478390_1_En_3_Fig2_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Fig2_HTML.jpg)

Figure 3-2

Spark Streaming

The earlier version of Spark offered a streaming API that was known as Spark Streaming (Dstream). Spark Streaming was based on RDDs (an earlier Spark abstraction before DataFrame/datasets) and had few limitations. As shown in Figure [3-3](#478390_1_En_3_Chapter.xhtml#Fig3), it was able to receive input data from various sources, such as Kafka, Flume, etc., and convert the incoming data into micro-batches and process them using Spark Engine.

![../images/478390_1_En_3_Chapter/478390_1_En_3_Fig3_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Fig3_HTML.jpg)

Figure 3-3

Spark Streaming data flow

The results of each batch would be generated as stream-only and would be saved to the output location. Each micro-batch was an RDD, based on certain time intervals, as shown in Figure [3-4](#478390_1_En_3_Chapter.xhtml#Fig4).

![../images/478390_1_En_3_Chapter/478390_1_En_3_Fig4_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Fig4_HTML.jpg)

Figure 3-4

Spark Streaming batch processing

Although the earlier Spark streaming component was quite powerful in terms of handling the streaming data processing, it was lacking in certain aspects.

1.  One core drawback was that there were different APIs for batch and stream data processing jobs. Lots of changes (translations) had to be made to convert a batch job into a Dstream job.

2.  It was unable to handle batch data processing based on event time, only on batch time. It was difficult to manage late arriving data for processing.

3.  It had limited fault tolerance capability , without any end-to-end guarantee of consistent data processing.

## Structured Streaming

The latest version of the streaming component in Spark is known as Structured Streaming, which is a huge improvement over the last RDD-based Spark streaming API. The first significant change from the previous version is that Structured Streaming offers the same API for batch as well as stream data processing jobs. Therefore, it works in a similar way for static and bounded batch data as for streaming and unbounded data, as shown in Figure [3-5](#478390_1_En_3_Chapter.xhtml#Fig5).

![../images/478390_1_En_3_Chapter/478390_1_En_3_Fig5_HTML.png](images/478390_1_En_3_Chapter/478390_1_En_3_Fig5_HTML.png)

Figure 3-5

Static vs. Streaming DataFrame

Another major shift from earlier versions is that Spark Structured Streaming is now built on top of the Spark SQL engine and uses DataFrame for multiple operations, such as aggregation, filters, etc. It also provides the end-to-end guarantee of data consistency, while writing the results in an output location. In order to understand how Structured Streaming works, let’s go through how data flows through its programming model, as shown in Figure [3-6](#478390_1_En_3_Chapter.xhtml#Fig6).

![../images/478390_1_En_3_Chapter/478390_1_En_3_Fig6_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Fig6_HTML.jpg)

Figure 3-6

Structured Streaming process

As the data arrives at time interval 1 (based on the window period selected), the input DataFrame consists of all the records up to that time interval (t=1). The next step is query execution (processing, transformation, join, aggregation) on that particular DataFrame (t=1). Once the query is completed, the results are made available, to be saved in the relevant output (console, memory, location). Now, new data arrives at time interval 2 and is added to the earlier DataFrame (t=1), resulting in a larger DataFrame (t=2). The query is again executed, but this time on a new DataFrame (t=2), and the results are saved in the selected output mode. This process continues for incoming streams of records, and each record is appended to the input DataFrame for data processing.

Now that we understand the basic process of data processing with Structured Streaming, we can consider the core pieces of a streaming-based application. There are three main areas into which we can divide this streaming framework:

1.  Data input

2.  Data processing (real time or near real time)

3.  Final output

### Data Input

Any streaming application requires data, to be able to ingest and process data continuously. Therefore, there are multiple ways to provide data as input to the Structured Streaming platform.

*   *Messaging systems*: Apache Kafka, Flume, and Logstash can be used to ingest real-time data and, hence, can easily become part of building streaming pipelines. The idea of using these tools is to capture all the data points as data is generated at the source application (web app, mobile app, IoT device) and pass it along to the Structured Streaming platform for further processing and analysis in a fault-tolerant and scalable manner.

*   *File folders/directory*: Files are read continuously from the directory as a stream of data. A scheduler can be used to put the new files into the directory. The files can be in text, Parquet, or JSON format. The only condition is to have all the files available in the same format.

### Data Processing

This is at the core of using streaming data for creating business value, as it can be applied to certain operations on the incoming data to get results. Such operations as aggregations, filtering, joins, sorting, etc., can be applied.

### Final Output

Structured Streaming provides multiple options for users to save their output results, as required, and it can either be in *Append* or *Complete* mode. Append mode refers to adding only new results to the final output table, whereas Complete mode updates the entire results table at the final output location.

1.  File directory sink

2.  Console

3.  Memory sink

## Building a Structured App

In this final section of the chapter, we now build a Structured Streaming app that can read files from the local system folder as new files are added to the folder as stream data and apply all the operations on the new data and, finally, write the results in an output directory. The first step is to create the `SparkSession` object, in order to use Spark.

```
[In]: from pyspark.sql import SparkSession
[In]: spark=SparkSession.builder.appName('structured_streaming').getOrCreate()
[In]: import pyspark.sql.functions as F
[In]: from pyspark.sql.types import *
```

Then, we create some self-generated data that can be pushed into a respective local directory (`"csv folder"`), to be read by the Structured Streaming. The data that we will generate contains four columns and is in CSV format. We can also generate a Parquet format, if required.

1.  User ID

2.  App

3.  Time spent (secs)

4.  Age

```
[In]:df_1=spark.createDataFrame([("XN203",'FB',300,30),("XN201",'Twitter',10,19),("XN202",'Insta',500,45)],["user_id","app","time_in_secs","age"]).write.csv("csv_folder",mode='append')
```

Once we have created these dataframes, we can define the schema of these files, in order to read them using stream processing.

```
[In]:schema=StructType().add("user_id","string").add("app","string").add("time_in_secs", "integer").add("age", "integer")
```

Now that we have one file available in the local folder (`"csv folder"`), we can go ahead and read it as a stream dataframe. The API to read a static dataframe is similar to that for reading a streaming dataframe, the only difference being that we use `readStream`.

```
[In]: data=spark.readStream.option("sep", ",").schema(schema).csv("csv_folder")
```

To validate the schema of the dataframe, we can use the `printSchema` command.

```
[In]: data.printSchema()
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figa_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figa_HTML.jpg)

### Operations

Once we have the streaming dataframe available, we can apply multiple transformations, in order to get different results, based on specific requirements. In this example, we are going to see aggregations, sorting, filters, etc. First, is simply to count the records of each app in the dataframe. We can write the command as if we are applying the transformations on the static dataframe.

```
[In]: app_count=data.groupBy('app').count()
```

In order to view the results, we must mention the output mode, in addition to the desired location. In this example, we write the results in memory, but it can be written to console, specific cloud storage, or any other location. We also give the output mode as complete, in order to write results on the entire dataframe every time. Finally, we use a simple Spark SQL command to view the output from the query we executed on the streaming dataframe, by converting to a Pandas dataframe.

```
[In]:query=(app_count.writeStream.queryName('count_query').outputMode('complete').format('memory').start())
[In]: spark.sql("select * from count_query ").toPandas().head(5)
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figb_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figb_HTML.jpg)

In this example, a query is being written to filter only the records of the Facebook (FB) app. The average time spent by each user on the FB app is then calculated.

```
[In]: fb_data=data.filter(data['app']=='FB')
[In]: fb_avg_time=fb_data.groupBy('user_id').agg(F.avg("time_in_secs"))
[In]:fb_query=(fb_avg_time.writeStream.queryName('fb_query').outputMode('complete').format('memory').start())
[In]: spark.sql("select * from fb_query ").toPandas().head(5)
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figc_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figc_HTML.jpg)

Because there is only one dataframe currently in the local folder, we get the output of one user accessing FB and the time spent. In order to view more relative results, let’s push more self-generated data to the folder.

```
[In]:df_2=spark.createDataFrame([("XN203",'FB',100,30),("XN201",'FB',10,19),("XN202",'FB',2000,45)],["user_id","app","time_in_secs","age"]).write.csv("csv_folder",mode='append')
```

We can now safely assume that Spark Structured Streaming has read the new records and appended them into the streaming dataframe and, therefore, the new results for the same query will differ from the last one.

```
[In]: spark.sql("select * from fb_query ").toPandas().head(5)
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figd_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figd_HTML.jpg)

Now, we have the average time spent across all users using the FB app. Let’s add few more records to the folder.

```
[In]:df_3=spark.createDataFrame([("XN203",'FB',500,30),("XN201",'Insta',30,19),("XN202",'Twitter',100,45)],["user_id","app","time_in_secs","age"]).write.csv("csv_folder",mode='append')
[In]: spark.sql("select * from fb_query ").toPandas().head(5)
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Fige_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Fige_HTML.jpg)

In this example, we see aggregation and sorting of the query on the existing dataframe in the local folder. We group all the records by app and calculate the total time spent on each app, in decreasing order.

```
[In]:app_df=data.groupBy('app').agg(F.sum('time_in_secs').alias('total_time')).orderBy('total_time',ascending=False)
[In]:app_query=(app_df.writeStream.queryName('app_wise_query').outputMode('complete').format('memory').start())
[In]: spark.sql("select * from app_wise_query ").toPandas().head(5)
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figf_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figf_HTML.jpg)

We now have the results for each app and the total time spent by all users on the respective app, using a stream dataframe. Let’s add new records one more time and see the revised results for the same query.

```
[In]:df_4=spark.createDataFrame([("XN203",'FB',500,30),("XN201",'Insta',30,19),("XN202",'Twitter',100,45)],["user_id","app","time_in_secs","age"]).write.csv("csv_folder",mode='append')
[In]: spark.sql("select * from app_wise_query ").toPandas().head(5)
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figg_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figg_HTML.jpg)

In this example, we try to find the average age of users for every app in our data. We simply group the data by app, take the average age of all the users, and sort the results in decreasing order.

```
[In]:age_df=data.groupBy('app').agg(F.avg('age').alias('mean_age')).orderBy('mean_age',ascending=False)
[In]:age_query=(age_df.writeStream.queryName('age_query').outputMode('complete').format('memory').start())
[In]:df_5=spark.createDataFrame([("XN210",'FB',500,50),("XN255",'Insta',30,23),("XN222",'Twitter',100,30)],["user_id","app","time_in_secs","age"]).write.csv("csv_folder",mode='append')
[In]: spark.sql("select * from age_query ").toPandas().head(5)
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figh_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figh_HTML.jpg)

So, in the preceding examples, we see how we can use Spark Structured Streaming to read the incoming data and create a streaming dataframe to apply various transformations and write the results in a particular location. One more common requirement on streaming data is joins.

### Joins

Sometimes we have to merge incoming data with batch data, to make it more comprehensive. In the following example, we will see how we can merge incoming data (stream dataframe) with a static dataframe that contains the full name of the apps. Let’s create a new static dataframe with two columns (app and full name).

```
[In]:app_df=spark.createDataFrame([('FB','FACEBOOK'),('Insta','INSTAGRAM'),('Twitter','TWITTER')],["app", "full_name"])
[In]: app_df.show()
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figi_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figi_HTML.jpg)

Now that we have a static dataframe available, we can simply write a new query to join the streaming dataframe (data) that we have been working with so far and merge both of them in an app column.

```
[In]: app_stream_df=data.join(app_df,'app')
[In]:join_query=(app_stream_df.writeStream.queryName('join_query').outputMode('append').format('memory').start())
[In]: spark.sql("select * from join_query ").toPandas().head(50)
[Out]:
```

![../images/478390_1_En_3_Chapter/478390_1_En_3_Figj_HTML.jpg](images/478390_1_En_3_Chapter/478390_1_En_3_Figj_HTML.jpg)

As you can see, we now have the additional column (`full_name`) in the streaming dataframe as well.

## Structured Streaming Alternatives

Since Spark Structured Streaming was made available in 2016, it has been rapidly gaining attention from the developers community. Having said that, however, there are a couple of other powerful alternatives to Spark’s Structured Streaming. One of them is Flink, which offers similar capability, in terms of streaming data processing with excellent latency rate. Another alternative is Google’s Beam, which is suitable in limited cases.

The core advantage of Spark’s Structured Streaming over the alternatives is the full-fledged framework provided by Spark as a whole, including batch processing (no major difference in code for batch or stream) and the machine learning library. Another great attribute of Structured Streaming is its Spark SQL API, which is extremely comfortable to many users. It is hoped that new versions of Spark will include more features for Structured Streaming, such as stream joins. Selection depends on the specific requirements of the application and finding the best possible alternative to make it scalable, fault-tolerant, and robust.

## Conclusion

In this chapter, the basic difference between batch and streaming data was identified. How Spark’s streaming API has evolved over the last few years to become the default framework for building streaming data applications was then discussed. Examples of how Spark’s Structured Streaming API is used to read streaming data (local folders), and how to save the aggregated results, were then provided.

# 4. Airflow

This chapter focuses on introducing Airflow and how it can be used to handle complex data workflows. Airflow was developed in-house by Airbnb engineers, to manage internal workflows in an efficient manner. Airflow later went on to become part of Apache in 2016 and was made available to users as an open source. Basically, Airflow is a framework for executing, scheduling, distributing, and monitoring various jobs in which there can be multiple tasks that are either interdependent or independent of one another. Every job that is run using Airflow must be defined via a directed acyclic graph (DAG) definition file, which contains a collection you want to run, grouped by relationships and dependencies.

This chapter concentrates on three main topics. I’ll start by examining workflow and then cover the basic building block of Airflow: DAG. You will then learn about the user interface aspect of Airflow. In the final section, I will go over the code to define DAG for a job and how to use Airflow to execute and monitor it.

## Workflows

Most of the things that we see around us follow a process. Trains run at set intervals, planes fly on fixed times, and signals on roads change at regular periods. Process is critical if consistency is required, especially when there are dependencies among different tasks. In the world of software or technology, a set process is also followed, in building or executing projects. If we go down a few levels, we can call these processes workflows. A proper workflow must be designed and executed, in order to achieve the desired results. It is rare to witness developers or engineers who don’t follow a standard procedure, to build a solution or application. For example, in a typical scenario for data processing workflow, developers will define the steps before executing the tasks, as shown in Figure [4-1](#478390_1_En_4_Chapter.xhtml#Fig1). These steps are often referred to as pipelines, which consist of sequences of tasks that must be completed.

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig1_HTML.jpg](images/478390_1_En_4_Chapter/478390_1_En_4_Fig1_HTML.jpg)

Figure 4-1

Sample workflow

The first task is to read the data from the source file and then copy it to your platform or desired location. Once data is made available, you ingest it and pass it through multiple steps to clean and transform it. You then perform the analysis and calculate the results. Finally, you save the output to the required location. Traditionally, the way these steps are executed is with the help of cron jobs (commands run by the cron daemon at scheduled intervals). So, all the tasks are part of the scripts which are run as cron jobs. Although, it serves the purpose of executing things in a particular order, it still faces many practical challenges. Most common is the breakdown of the script execution. There is no clear methodology to re-attempt the script run. Another challenge is monitoring the status of the running job. It is very difficult to determine the stage and the time duration each stage takes while executing. There are other challenges as well, such as running multiple cron jobs through a centralized scheduler, for bigger and complex pipelines and to accommodate continuous changes in the workflow. Owing to all of the difficulties mentioned, Airflow was born as a framework with which to schedule and monitor such kinds of jobs and run the pipelines smoothly, for consistent output. To truly understand how Airflow and DAGs operate, you first must understand graphs in general.

## Graph Overview

A typical graph data structure consists of two entities, shown in Figure [4-2](#478390_1_En_4_Chapter.xhtml#Fig2).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig2_HTML.jpg](images/478390_1_En_4_Chapter/478390_1_En_4_Fig2_HTML.jpg)

Figure 4-2

Structure of a graph

1.  Edges

2.  Nodes/vertices

The edges are essentially the connections between the nodes/vertices, and nodes are where actual data resides. We can place graph-based networks into two main categories:

1.  Undirected graphs

2.  Directed graphs

### Undirected Graphs

In this kind of graph structure, the edges or connections don’t have any direction, as in Figure [4-2](#478390_1_En_4_Chapter.xhtml#Fig2). The relationship will exist at both ends. For example, if person 1 in node 1 is a friend of person 2 in node 2, then person 2 would also be a friend of person 1.

### Directed Graphs

A directed graph is either cyclic or acyclic. The direction of the edge plays an important role, as all the edges in a graph go only one way, as in Figure [4-3](#478390_1_En_4_Chapter.xhtml#Fig3).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig3_HTML.jpg](images/478390_1_En_4_Chapter/478390_1_En_4_Fig3_HTML.jpg)

Figure 4-3

Directed graph

Cyclic graphs have one or more cycles. A cycle is a path that begins and ends at the same node, as shown in Figure [4-4](#478390_1_En_4_Chapter.xhtml#Fig4). The information flows from node 1 to node 2, but there is another way back from node 2 to node 1\. This is known as a cycle, or loop, graph.

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig4_HTML.jpg](images/478390_1_En_4_Chapter/478390_1_En_4_Fig4_HTML.jpg)

Figure 4-4

Cyclic graph

There are no cycles in DAGs, so their benefits include the following:

*   A dynamic framework (configuration as code)

*   Extensibility—They support different types of task execution.

*   Scalability—They can perform an infinite number of tasks (worker nodes).

Let’s now turn our attention to DAGs.

## DAG Overview

Because a DAG is a directed graph, information can flow in only one direction, and that’s forward, as illustrated in Figure [4-5](#478390_1_En_4_Chapter.xhtml#Fig5). So, if node 4 must be reached, the path is 1 ➤ 2 ➤ 4\. In DAGs, there is no reverse path back to the starting node.

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig5_HTML.jpg](images/478390_1_En_4_Chapter/478390_1_En_4_Fig5_HTML.jpg)

Figure 4-5

Flow of information in a DAG

All the tasks of jobs in Airflow must be defined in a DAG. So, the order of execution is defined in DAG form. For example, if task 8 has to be executed, task 1 and task 3 must be finished first, whereas for task 7 to be completed, there doesn’t have to be any dependence on other tasks, apart from task 3\. Therefore, some of the independent tasks (2, 3) can take place irrespective of each other’s state as shown in Figure [4-5](#478390_1_En_4_Chapter.xhtml#Fig5). The order of execution of the tasks and interdependencies can be defined well before executing them with Airflow.

All the configurations related to DAG are defined in a DAG definition file, which is a Python extension. It contains all the dependencies and configuration parameters, such as e-mail to be sent in case of failures, start time, end time, and number of retries. We also have to define all the tasks that are part of the DAG, in addition to the dependencies or sequence of the tasks.

### Operators

As discussed previously, a DAG can contain multiple tasks. These tasks can be totally different from one another. One of the tasks can be a simple Python script; another can be a shell script or SQL query; and another can be a cloud-based Spark job. These tasks are defined inside of DAG definition file, using operators. Airflow provides a range of operators for different types of tasks. The most common ones are the following:

1.  Python operator (Python script)

2.  Bash operator (Shell script)

3.  SQL operator

4.  Docker operator

5.  Cloud operator (S3, Azure, Google)

This flexibility of Airflow to run any type of task makes it very powerful, compared to other schedulers.

A DAG file is simply a Python script that contains all the required configuration parameters to run the DAG. There are a few standard steps that must be taken, in order to create a DAG, as shown following:

1.  Importing required modules

2.  Declaring default arguments

3.  Instantiating the DAG object

4.  Defining all the tasks

5.  Declaring the order of execution/task dependencies

In some areas, Airflow stands out, compared to alternatives, in terms of handling upstream and downstream dependencies more intelligently and in its ability to run historical loads. It also handles failures and blockers with complete transparency.

## Installing Airflow

There are multiple ways in which Airflow can be used, as it is designed to easily integrate with different environments. If we want to use Airflow locally, we can install and configure the Airflow environment locally. The other option is to make use of Docker, to containerize the Airflow application and run it on any platform, irrespective of the environment. The core benefit of using Docker is that it takes on itself the additional burden of managing dependencies and deployment.

### Airflow Using Docker

One way to run Airflow is to create your own Docker image, with all the dependencies and components, but an Airflow Docker image that makes it very easy to run is already available from the Docker Hub. The steps to run Airflow are as follows:

```
[In]: docker pull puckel/docker-airflow
[In]:docker run -d -p 8080:8080 puckel/docker-airflow webserver
```

Once you run the preceding command in a terminal, you can access port 8080, by going to `http://127.0.0.1:8080`, as shown in Figure [4-6](#478390_1_En_4_Chapter.xhtml#Fig6).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig6_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig6_HTML.png)

Figure 4-6

Airflow UI

This is how the Airflow UI looks. It currently holds no DAGs and is a vanilla version. If you want to see the sample DAGs that come in Airflow by default, you simply have to add one extra argument while running Docker. To see which containers are running, we can use the following code:

```
[In]: docker ps
```

Once all the containers are listed, you can stop/kill the earlier container running Airflow, by using

```
[In]: docker kill 
```

Now we run the following command with `LOAD_EX=y` as an additional parameter, as follows:

```
[In]: docker run -d -p 8080:8080 -e LOAD_EX=y puckel/docker-airflow
```

If we access the Airflow UI now, we get a list of all default DAGs, as shown in Figure [4-7](#478390_1_En_4_Chapter.xhtml#Fig7).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig7_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig7_HTML.png)

Figure 4-7

Airflow DAGs

#### Airflow Setup (Mac)

The first step is to ensure that Python is installed on the machine, and we can use the `brew install` command to install `python3`. Once Python is installed, we can install Airflow. It requires a home directory. `~/airflow` is the default, but a different location can also be chosen, based on preference.

```
[In]:  brew install python python3
[In]: pip install airflow
[In]: mkdir ~/airflow
[In]: export AIRFLOW_HOME=~/airflow
[In]: cd ~/airflow
[In]: airflow initdb
[In]: airflow webserver -p 8080
```

The final two steps are to initialize Airflow, using `initdb`, and accessing the UI on the preferred port.

## Creating Your First DAG

As discussed in the earlier part of the chapter, a DAG consists of multiple tasks arranged in a particular order, as shown in Figure [4-8](#478390_1_En_4_Chapter.xhtml#Fig8).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig8_HTML.jpg](images/478390_1_En_4_Chapter/478390_1_En_4_Fig8_HTML.jpg)

Figure 4-8

Tasks

In order to create a DAG, you must define a DAG file that contains all the details pertaining to DAG tasks, and dependencies must be defined in a file (Python script). This is a configuration file specifying the DAG’s structure as code. The five steps that must to be taken to run a DAG are shown in Figure [4-9](#478390_1_En_4_Chapter.xhtml#Fig9).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig9_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig9_HTML.png)

Figure 4-9

Airflow steps

Let’s go into detail about each of these steps, to understand the internals better.

### Step 1: Importing the Required Libraries

The first step is to import all the required libraries for running Airflow. Some common ones include datetime, different operators (Bash/Python), and Airflow itself.

```
[In]: from datetime import timedelta
[In]: import airflow
[In]: from airflow import DAG
[In]: from airflow.operators.bash_operator import BashOperator
```

### Step 2: Defining the Default Arguments

The next step is to define some important parameters, to ensure that Airflow executes the DAGs at designated time intervals and an appropriate number of times.

```
[In]: args = {
'owner': 'Pramod',
'start_date': airflow.utils.dates.days_ago(3),
# 'end_date': datetime(2018, 12, 30),
'depends_on_past': False,
'email': ['airflow@example.com'],
'email_on_failure': False,
'email_on_retry': False,
'retries': 1,
'retry_delay': timedelta(minutes=5),
}
```

### Step 3: Creating a DAG

The third step is to create the DAG itself, which consists of the DAG’s name and schedule interval, as shown following. You can decide when to run the jobs, depending on your requirements.

```
[In]: dag = DAG(
'pramod_airflow_dag',
default_args=args,
description='A simple DAG',
# Continue to run DAG once per day
schedule_interval=timedelta(days=1)
```

### Step 4: Declaring Tasks

The next step is to declare the tasks (actual jobs) to be executed. All the tasks can be declared and made part of the same DAG created in the preceding step.

```
[In]: t1 = BashOperator(
task_id='print_date',
bash_command='date',
dag=dag,
)
t2 = BashOperator(
task_id='sleep',
depends_on_past=False,
bash_command='sleep 5',
dag=dag,
)
```

### Step 5: Mentioning Dependencies

The final step is to set the order of task execution. They can be either parallel or sequential tasks. There are multiple ways in which the tasks can be defined.

```
[In]: t1 >> t2
```

Once all the preceding steps have been completed, we can start Airflow and access the web UI. The screen shown in Figure [4-10](#478390_1_En_4_Chapter.xhtml#Fig10) is available with the DAG file just created.

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig10_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig10_HTML.png)

Figure 4-10

Airflow DAG

Currently, it’s in OFF stage, and we can either trigger it manually or through a command-line interface (CLI). If we click the DAG itself, it takes us to the default tree view of the DAG, which lists all the tasks within the DAG, as shown in Figure [4-11](#478390_1_En_4_Chapter.xhtml#Fig11).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig11_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig11_HTML.png)

Figure 4-11

Tree view

Now switch on the DAG, in order to initiate the tasks, as shown in Figure [4-12](#478390_1_En_4_Chapter.xhtml#Fig12).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig12_HTML.jpg](images/478390_1_En_4_Chapter/478390_1_En_4_Fig12_HTML.jpg)

Figure 4-12

DAG initialization

Then click the Trigger Dag icon and start the execution, as shown in Figure [4-13](#478390_1_En_4_Chapter.xhtml#Fig13).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig13_HTML.jpg](images/478390_1_En_4_Chapter/478390_1_En_4_Fig13_HTML.jpg)

Figure 4-13

DAG trigger

The moment DAG is triggered, we can see the change under the Recent Tasks tab, and DAG runs, as shown in Figure [4-14](#478390_1_En_4_Chapter.xhtml#Fig14).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig14_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig14_HTML.png)

Figure 4-14

Running the DAG

Once the DAG starts to run we can view it in different ways , the first form is the Tree View as shown in Figure [4-15](#478390_1_En_4_Chapter.xhtml#Fig15).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig15_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig15_HTML.png)

Figure 4-15

Airflow UI

Another view is the Graph View that shows the order of task execution in a slightly different manner, and the color of the task indicates its progress (success/running/failed, etc.), as shown in Figure [4-16](#478390_1_En_4_Chapter.xhtml#Fig16).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig16_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig16_HTML.png)

Figure 4-16

Graph view

We can also drill deeper into other details of the DAG, by going to the DAG details tab, shown in Figure [4-17](#478390_1_En_4_Chapter.xhtml#Fig17).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig17_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig17_HTML.png)

Figure 4-17

DAG details tab

Finally, under the Code tab, we can review the DAG Python script that was originally created before running the DAG, as shown in Figure [4-18](#478390_1_En_4_Chapter.xhtml#Fig18).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig18_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig18_HTML.png)

Figure 4-18

DAG code

If we wait a while and recheck Graph View, we will observe that both tasks have been completed successfully, as illustrated in Figure [4-19](#478390_1_En_4_Chapter.xhtml#Fig19). From the Tree View tab, we can see that tasks have been completed for all three days (the last two and today’s), as shown in Figure [4-20](#478390_1_En_4_Chapter.xhtml#Fig20).

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig20_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig20_HTML.png)

Figure 4-20

Tree view

![../images/478390_1_En_4_Chapter/478390_1_En_4_Fig19_HTML.png](images/478390_1_En_4_Chapter/478390_1_En_4_Fig19_HTML.png)

Figure 4-19

Graph view

## Conclusion

In this chapter, you learned how to define a DAG and run different jobs using Airflow. The steps to implement Airflow and monitor the status of different tasks were also covered. You were also introduced to the web UI for Airflow, in addition to different components within the interface.

# 5. MLlib: Machine Learning Library

Depending on your requirements, there are multiple ways in which you can build machine learning models, using preexisting libraries, such as Python’s scikit-learn, R, and TensorFlow. However, what makes Spark’s Machine Learning library (MLlib) really useful is its ability to train models on scale and provide distributed training. This allows users to quickly build models on a huge dataset, in addition to preprocessing and preparing workflows with the Spark framework itself.

This chapter focuses on how to leverage MLlib for building and applying various machine learning models. The first part focuses on basic statistics, using MLlib, followed by building pipelines to create features and other transformations. The last part of the chapter discusses using MLlib for building machine learning classification models.

Let’s begin by reviewing how we can use Spark’s MLlib for calculating some of the basic statistical measures for data analysis. You will see how to calculate correlations between two numerical variables and how to use a chi-square test to determine if there is a significant relationship between two categorical variables.

## Calculating Correlations

Correlation is an important metric with which to determine if there is any relationship between two continuous variables. Correlation can either be positive or negative, as shown in Figure [5-1](#478390_1_En_5_Chapter.xhtml#Fig1). It is also possible for there to be no correlation between two variables.

![../images/478390_1_En_5_Chapter/478390_1_En_5_Fig1_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Fig1_HTML.jpg)

Figure 5-1

Types of correlations

Correlation is very easy to calculate using Spark MLlib. It provides the options to calculate two types of coefficients of correlations:

1.  Pearson

2.  Spearman

Using Spark, let’s take a sample dataframe, to calculate the coefficient of correlation. This dataset contains just two numerical columns (Years Experience and Salary). The first step is to create the Spark context, in order to use Spark.

```
[In]: from pyspark.sql import SparkSession
[In]: spark=SparkSession.builder.appName('basic_stats').getOrCreate()
[In]: df=spark.read.csv('corr_data.csv',header=True,inferSchema=True)
[In]: df.count()
[Out]: 30
[In]: df.show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figa_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figa_HTML.jpg)

As you can see, there are just 30 records in this dataframe. Next, we combine the two columns into a single dense vector representation, in order to calculate the correlation coefficient, using VectorAssembler. We name the new dense vector “features.”

```
[In]: from pyspark.ml.feature import VectorAssembler
[In]: assembler = VectorAssembler(inputCols=df.columns, outputCol="features")
[In]: df_new=assembler.transform(df)
[In]: df_new.show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figb_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figb_HTML.jpg)

```
Pearson Coefficient of Correlation
[In]: from pyspark.ml.stat import Correlation
[In]: pearson_corr = Correlation.corr(df_new,'features')
[in]: pearson_corr.show(2,False)
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figc_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figc_HTML.jpg)

```
Spearman Coefficient of Correlation
[In]: spearman_corr=Correlation.corr(df_new,'features',"spearman")
[In]: spearman_corr.show(2,False)
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figd_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figd_HTML.jpg)

### Chi-Square Test

Correlation is all about the relationship between numerical features, whereas other types of variables can be categorical as well. One of the ways to validate the relationship between two categorical variables is through a chi-square test. Let’s consider an example, to understand how it works. Some made up data is summarized in Table [5-1](#478390_1_En_5_Chapter.xhtml#Tab1).

Table 5-1

Sample Data

|   | Smoker | Nonsmoker |
| --- | --- | --- |
| Teen | 32 | 12 |
| Young | 14 | 22 |
| Old | 6 | 9 |

It contains three categories of people (Teen, Young, and Old) and is divided into two buckets (Smoker and Nonsmoker). In the next step, we calculate the total number of people in each category and bucket, as shown in Table [5-2](#478390_1_En_5_Chapter.xhtml#Tab2). This is also known as a contingency table.

Table 5-2

Contingency Table

|   | Smoker | Nonsmoker | Total |
| --- | --- | --- | --- |
| Teen | 32 | 12 | 44 |
| Young | 14 | 22 | 36 |
| Old | 6 | 9 | 15 |
|   | 52 | 43 | 95 |

The next step is to calculate the expected values, based on the actual values in the table. The expected values are calculated with the following formula:

![$$ xpected\ {Value}_{Teen- Smoker}=\left({Total}_{Teen}\ast \kern0.5em {Total}_{Smoker}\right)/ Total $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Equa.png)

![$$ Expected\ {Value}_{Teen- Smoker}=\left(44\ast \kern0.5em 52\right)/95 $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Equb.png)

![$$ Expected\ {Value}_{Teen- Smoker}=24 $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Equc.png)

![$$ {x}^2 $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Equd.png)

Similarly, we calculate all the expected values for each category against both buckets, as shown in Table [5-3](#478390_1_En_5_Chapter.xhtml#Tab3).

Table 5-3

Expected Values

|   | Smoker | Nonsmoker |
| --- | --- | --- |
| Teen | 24 | 20 |
| Young | 20 | 16 |
| Old | 8 | 7 |

The next step is to arrive at a chi-square table, by comparing the actual values against expected values. The chi-square table values are calculated with the following formula:

![$$ Chi\ Square\ Table\ {Value}_{Teen- Smoker}={\left({Actual}_{Teen- Smoker}-\kern0.5em {Expected}_{Teen- Smoker}\right)}^2/{Expected}_{Teen- Smoker} $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Eque.png)

![$$ Chi\ Square\ Table\ {Value}_{Teen- Smoker}=\frac{{\left(32-24\right)}^2}{24} $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Equf.png)

![$$ Chi\ Square\ Table\ {Value}_{Teen- Smoker}=2.602 $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Equg.png)

Now that we have the chi-square values, we take the total for each bucket (Smoker and Nonsmoker), as shown in Table [5-4](#478390_1_En_5_Chapter.xhtml#Tab4).

Table 5-4

Chi-Square Totals

|   | Smoker | Nonsmoker |
| --- | --- | --- |
| Teen | 2.602 | 3.146 |
| Young | 1.652 | 1.998 |
| Old | 0.595 | 0.720 |
| Chi-square value | 4.849 | 5.864 |

The overall chi-square value comes to 10.7 (4.84 + 5.86). We then look up for the value of 10.7 in the chi-square table for the degree of freedom (3-1)∗(2-1) = 2 and find the corresponding p value. If the p value is less than 0.05, this indicates a statistically significant relationship between the two variables. Let’s try to run a chi-square test using Spark. Here we have a sample dataset that has three columns, but we will try to determine if there is any relationship between the marital and housing columns (both are categorical in nature).

```
[In]: df=spark.read.csv('chi_sq.csv',inferSchema=True,header=True)
[In]: df.count()
[Out]: 9501
[In]: df.show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Fige_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Fige_HTML.jpg)

```
[In]: from pyspark.ml.feature import StringIndexer
[In]: marital_indexer = StringIndexer(inputCol="marital", outputCol="marital_num").fit(df)
[In]: df = marital_indexer.transform(df)
[In]: from pyspark.ml.feature import OneHotEncoder
[In]: marital_encoder = OneHotEncoder(inputCol="marital_num", outputCol="marital_vector")
[In]: df = marital_encoder.transform(df)
[In]: housing_indexer = StringIndexer(inputCol="housing", outputCol="housing_num").fit(df)
[In]: df = housing_indexer.transform(df)
[In]: housing_encoder = OneHotEncoder(inputCol="housing_num", outputCol="housing_vector")
[In]: df = housing_encoder.transform(df)
[In]: df.show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figf_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figf_HTML.jpg)

```
[In]: df_assembler = VectorAssembler(inputCols=['marital_vector','housing_vector'], outputCol="features")
[In]: df = df_assembler.transform(df)
[In]: df.show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figg_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figg_HTML.jpg)

```
[In]: chi_sq = ChiSquareTest.test(df, "features", "label").head()
[In]: print("pValues: " + str(chi_sq.pValues))
[Out]:
pValues: [0.0,0.0,0.06036632491,0.0,3.56381590905e-14]
```

## Transformations

In this section, I will go over some of the common transformations required for data preprocessing and feature engineering. These help in preparing data the right way for applying machine learning.

### Binarizer

We can convert the numerical/continuous variable into categorical features (0/1) by using Binarizer in MLlib. We must declare the threshold value, in order to convert the numerical feature into a binary feature. Any value above the threshold will be converted into 1, and values below or equal to the threshold will become 0\. Let’s apply Binarizer on the label column of the transformation sample dataset.

```
[In]: df=spark.read.csv('transformations.csv',header=True,inferSchema=True)
[In]: df.count()
[Out]: 6366
[In]: df.show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figh_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figh_HTML.jpg)

Now we import the Binarizer function from the Spark library.

```
[In]: from pyspark.ml.feature import Binarizer
[In]: binarizer = Binarizer(threshold=0.99, inputCol="label", outputCol="binarized_label")
[In]: new_df=binarizer.transform(df)
[In]: new_df.show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figi_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figi_HTML.jpg)

### Principal Component Analysis

Most of the time, we deal with multidimensional data, and it sometimes becomes difficult to understand the underlying pattern without visualizing that data. Principal component analysis (PCA) is one of the transformation techniques that allows you to reduce the dimensions of the data while keeping intact the variation of the data as much as possible. Let’s go over the steps to apply PCA, on the same data used previously.

```
[In]: from pyspark.ml.feature import PCA
[In]: assembler = VectorAssembler(inputCols=[col for col in df.columns if col !='label'], outputCol="features")
[In]: df_new=assembler.transform(df)
[In]: df_new.show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figj_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figj_HTML.jpg)

`k` represents the number of reduced dimensions of the data after PCA.

```
[In]: pca = PCA(k=2, inputCol="features", outputCol="pca_features")
[In]: pca_model=pca.fit(df_new)
[In]: pca_comp = pca_model.transform(df_new).select("pca_features")
[In]: pca_comp.show(truncate=False)
[out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figk_HTML.png](images/478390_1_En_5_Chapter/478390_1_En_5_Figk_HTML.png)

As you can see, we have applied PCA to the data (except for the “label” column) and reduced the number of dimensions to just two.

### Normalizer

Normalization refers to transformation of data in such a way that the new normalized data has a mean of 0 and a standard deviation of 1\. The normalization is done using the following formula:

![$$ \frac{\Big(\left(x- mean(x)\right)}{standard\ dev\ (x)} $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Equh.png)

To make use of Normalizer in Spark, we simply have to apply it on the required column. Here, we apply it to the “features” column.

```
[In]: from pyspark.ml.feature import Normalizer
[In]: normalizer = Normalizer(inputCol="features", outputCol="norm_features", p=1.0)
[In]: normalised_l1_data = normalizer.transform(df_new)
[In]: normalised_l1_data.select('norm_features').show(truncate=False)
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figl_HTML.png](images/478390_1_En_5_Chapter/478390_1_En_5_Figl_HTML.png)

The normalization helps standardize the input data and sometimes improve the performance of the machine learning models.

### Standard Scaling

Scaling is another technique to normalize data, such that the values are within a specific range, e.g., [0, 1]. Many machine learning algorithms are sensitive to the scale of the input data, and, hence, it becomes critical to apply scaling. Scaling can be applied in different ways, but the most fundamental approach is to use the following formula:

![$$ \frac{x-\mathit{\min}(x)}{\mathit{\max}\ (x)-\min (x)} $$](images/478390_1_En_5_Chapter/478390_1_En_5_Chapter_TeX_Equi.png)

```
[In]: from pyspark.ml.feature import StandardScaler
[In]: scaler = StandardScaler(inputCol="features", outputCol="scaled_features",
withStd=False, withMean=True)
[In]: scaler_model = scaler.fit(df_new)
[In]: scaled_data = scaler_model.transform(df_new)
[In]: scaled_data.select('scaled_features').show(truncate=False)
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figm_HTML.gif](images/478390_1_En_5_Chapter/478390_1_En_5_Figm_HTML.gif)

### Min-Max Scaling

Min-max scaling is another version of standard scaling, as it allows you to rescale the feature values between specific limits (mostly, between 0 and 1). You can also rescale the values between 0 and 1, using min-max scaling.

```
[In]: from pyspark.ml.feature import MinMaxScaler
[In]: mm_scaler = MinMaxScaler(inputCol="features", outputCol="mm_scaled_features")
[In]: mm_scaler_model = mm_scaler.fit(df_new)
[In]: rescaled_df = mm_scaler_model.transform(df_new)
[In]: rescaled_df.select("features", "mm_scaled_features").show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Fign_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Fign_HTML.jpg)

To access the min and max values, we can use the `getMin` and `getMax` functions. In order to change the range, we can define the new min and max values by creating a scaler object. Here we rescale the values between -1 and 1.

```
[In]: mm_scaler.getMin()
[Out]: 0.0
[In]: mm_scaler.getMax()
[Out]: 1.0
Alter the min max values
[In]: mm_scaler = MinMaxScaler(inputCol="features", outputCol="mm_scaled_features”, min=-1,max=1)
[In]: mm_scaler_model = mm_scaler.fit(df_new)
[In]: rescaled_df = mm_scaler_model.transform(df_new)
[In]: rescaled_df.select("features", "mm_scaled_features").show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figo_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figo_HTML.jpg)

### MaxAbsScaler

MaxAbsScaler is a little different from standard scaling tools, as it rescales each feature value between -1 and 1\. However, it does not shift the center of the data and, hence, does not impact any sparsity.

```
[In]: from pyspark.ml.feature import MaxAbsScaler
[In]: mxabs_scaler = MaxAbsScaler(inputCol="features", outputCol="mxabs_features")
[In]: mxabs_scaler_model = mxabs_scaler.fit(df_new)
[In]: rescaled_df = mxabs_scaler_model.transform(df_new)
[In]: rescaled_df.select("features", "mxabs_features").show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figp_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figp_HTML.jpg)

### Binning

Binning, or bucketing , is useful in cases in which you want to group continuous features into categories. You can do binning with the help of Bucketizer in Spark. Let’s try to bucketize the target (label) column into bins. The splits can be made accordingly, but it’s always advisable to start and end the splits with negative infinity to positive infinity, to avoid out of bound errors (especially in cases in which max and min values of the feature are unknown).

```
[In]: from pyspark.ml.feature import Bucketizer
[In]: df.show(10,False)
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figq_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figq_HTML.jpg)

We now define the end points for the splits to occur and create a new column that contains the bins.

```
[In]: splits = [0.0,1.0,2.0,3.0,4.0,5.0,float("inf")]
[In]: bucketizer = Bucketizer(splits=splits, inputCol="label", outputCol="label_bins")
[In]: binned_df = bucketizer.transform(df)
[In]: binned_df.select(['label','label_bins']).show(10,False)
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figr_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figr_HTML.jpg)

As you can see, all the values have been put under a bin, and we can use `groupby` to validate the total bins (6). We can also get the bin values using the `getSplit` function.

```
[In]: binned_df.groupBy('label_bins').count().show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figs_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figs_HTML.jpg)

```
[In]: print(bucketizer.getSplits())-1
[Out]: 6
```

## Building a Classification Model

In this section, you will see how we can use Spark’s Machine Learning library (MLlib) to build classification models. Because there are dedicated chapters for supervised and unsupervised ML models later in the book, in this section, I will not go too deep into the details but focus instead on the overall process of building a model with the MLlib. For our example, we will use the data inspired by the dataset provided by Giulio Palombo in his book *A Collection of Data Science Take-Home Challenges*. The dataset contains information pertaining to a few customers who have applied for new bank loans and whether they will default. We will build a binary classification model to predict whether a particular customer should be granted a loan, based on the knowledge gleaned from the model. The following core steps are used to build a classification model:

1.  Load the dataset.

2.  Perform exploratory data analysis.

3.  Perform required data transformations.

4.  Split data into train and test subsets.

5.  Train and evaluate the baseline model on train data.

6.  Perform hyperparameter tuning.

7.  Build a final model with the best parameters.

### Step 1: Load the Dataset

In the first step, we initiate the Spark object, to use Spark and load the dataset to create the Spark dataframe.

```
[In]: from pyspark.sql import SparkSession
[In]: spark=SparkSession.builder.appName('binary_class').getOrCreate()
[In]: df=spark.read.csv('classification_data.csv',inferSchema=True,header=True)
```

### Step 2: Explore the Dataframe

In this step, we explore the different aspects of the data and various columns in the dataframe.

```
[In]: print((df.count(),len(df.columns)))
[Out]: (46751, 12)
```

The dataframe contains 12 columns and more than 46,000 records. We can view all the columns and datatypes, using the `printSchema` function.

```
[In]: df.printSchema()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figt_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figt_HTML.jpg)

We can use the `show` or `display` function to view the top few rows of the dataframe.

```
[In]: df.show(5)
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figu_HTML.png](images/478390_1_En_5_Chapter/478390_1_En_5_Figu_HTML.png)

We can use `groupby` to count the number of positive and negative events in the target column (label).

```
[In]: df.groupBy('loan_label).count().show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figv_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figv_HTML.jpg)

As you can see, more than one-third of all customers have defaulted on their loans. To understand the data better, we continue with exploratory data analysis. In the following results, we can see that people prefer to apply for a loan mainly for property, operations, and personal reasons.

```
[In]: df.groupBy('loan_purpose').count().show()
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figw_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figw_HTML.jpg)

Customers seem to have applied for property loans more than any other category of loan.

### Step 3: Data Transformation

Because all of the variables in the dataframe are numerical, except for the loan purpose, we must convert them into numerical form, using `OneHotEncoder`.

```
[In]: from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler
[In]: loan_purpose_indexer = StringIndexer(inputCol="loan_purpose", outputCol="loan_index").fit(df)
[In]: df = loan_purpose_indexer.transform(df)
[In]: loan_encoder = OneHotEncoder(inputCol="loan_index", outputCol="loan_purpose_vec")
[In]: df = loan_encoder.transform(df)
[In]: df.select(['loan_purpose','loan_index','loan_purpose_vec']).show(3,False)
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figx_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figx_HTML.jpg)

Now that we have converted the original loan-purpose feature into vectorized form, we can use `VectorAssembler` to create a single-feature vector for model training.

```
[In]: from pyspark.ml.feature import VectorAssembler
[In]: df_assembler = VectorAssembler(inputCols=['is_first_loan',
'total_credit_card_limit',
'avg_percentage_credit_card_limit_used_last_year',
'saving_amount',
'checking_amount',
'is_employed',
'yearly_salary',
'age',
'dependent_number',
'loan_purpose_vec'], outputCol="features")
[In]: df = df_assembler.transform(df)
[In]: df.select(['features','label]).show(10,False)
[Out]:
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figy_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figy_HTML.jpg)

We now create a new dataframe with just two columns: features and label.

```
[In]: model_df=df.select(['features','label'])
```

### Step 4: Splitting into Train and Test Data

We now split the overall data into training and test sets randomly, to avoid any bias in the training.

```
[In]: training_df,test_df=model_df.randomSplit([0.75,0.25])
```

### Step 5: Model Training

Now that our training and test data are ready, we can go ahead and train a baseline model, such as logistic regression, with default parameters and check its performance on train and test data.

```
[In]: from pyspark.ml.classification import LogisticRegression
[In]: log_reg=LogisticRegression().fit(training_df)
[In]: lr_summary=log_reg.summary
[In]: lr_summary.accuracy
[Out]: 0.8939298586875679
[In]: lr_summary.areaUnderROC
0.9587456481363935
[In]: print(lr_summary.precisionByLabel)
[Out]: [0.9233245149911816, 0.8396318618667535]
[In]: print(lr_summary.recallByLabel)
[Out]: [0.914054997817547, 0.8556606905710491]
[In]: predictions = log_reg.transform(test_df)
[In]: predictions.show(10)
```

![../images/478390_1_En_5_Chapter/478390_1_En_5_Figz_HTML.jpg](images/478390_1_En_5_Chapter/478390_1_En_5_Figz_HTML.jpg)

```
[In]: model_predictions = log_reg.transform(test_df)
[In]: model_predictions = log_reg.evaluate(test_df)
[In]: model_predictions.accuracy
[Out]: 0.8945984906300347
[In]: model_predictions.areaUnderROC
[Out]: 0.9594316478468224
[In]: print(model_predictions.recallByLabel)
[Out]: [0.9129581151832461, 0.8608235010835541]
[In]: print(model_predictions.precisionByLabel)
[Out]: [0.9234741162452006, 0.8431603773584906]
```

### Step 6: Hyperparameter Tuning

So, using a baseline model , we are getting almost 89% accuracy on the test data, and a recall rate of 0.86\. Now that we have built the baseline model, we can build a more sophisticted model, such as a random forest model, which is an ensemble method that can improve the accuracy of predictions. You will see how we can tune this model, to find the best possible hyper-parameters.

```
[In]: from pyspark.ml.classification import RandomForestClassifier
```

First, we build a random forest model with default hyper-parameters, then train it on the training data, so that predictions can be made on the test data.

```
[In]: rf = RandomForestClassifier()
[In]: rf_model = rf.fit(training_df)
[In]: model_predictions = rf_model.transform(test_df)
```

Using cross-validation techniques, we now try to come up with the best hyperparameters for this model.

```
[In]: from pyspark.ml.tuning import ParamGridBuilder, CrossValidator
[In]: from pyspark.ml.evaluation import BinaryClassificationEvaluator
[In]: evaluator = BinaryClassificationEvaluator()
[In]: rf = RandomForestClassifier()
[In]: paramGrid = (ParamGridBuilder()
.addGrid(rf.maxDepth, [5,10,20,25,30])
.addGrid(rf.maxBins, [20,30,40 ])
.addGrid(rf.numTrees, [5, 20,50])
.build())
```

We define the parameter grid with all the possible values for different hyperparameters (`maxDepth`, `maxBins`, `numTrees`) and apply cross-validation, to dtermine the best model. We use five-fold cross-validation in this case (four parts for training, and one for testing).

```
[In]: cv = CrossValidator(estimator=rf, estimatorParamMaps=paramGrid, evaluator=evaluator, numFolds=5)
[In]: cv_model = cv.fit(training_df)
```

We then access the best model parameters and use them on the test dataset, to make predictions.

### Step 7: Best Model

```
[In]: best_rf_model = cv_model.bestModel
[In]: model_predictions = best_rf_model.transform(test_df)
[In]:true_pos=model_predictions.filter(model_predictions['label']==1).filter(model_predictions['prediction']==1).count()
[In]:actual_pos=model_predictions.filter(model_predictions['label']==1).count()
[In]:pred_pos=model_predictions.filter(model_predictions['prediction']==1).count()
[In]: recall_rate=float(true_pos)/(actual_pos)
[In]: print(recall_rate)
[Out]: 0.912426614481409
```

As you can see from the preceding, with the random forest model with best hyperparameters, the recall rate has improved, compared to the baseline method (logistic regression).

## Conclusion

In this chapter, some transformation techniques using PySpark and ways to compute summary statistics were reviewed. You saw how to build a machine learning model from scratch and how to tune hyperparameters, to choose the best parameters for a model.

# 6. Supervised Machine Learning

Machine learning can be broadly divided into four categories: supervised machine learning and unsupervised machine learning and, to a lesser extent, semi-supervised machine learning and reinforcement machine learning. Because supervised machine learning drives a lot of business applications and significantly affects our day-to-day lives, it is considered one of the most important categories.

This chapter reviews supervised machine learning, using multiple algorithms. In Chapter [7](#478390_1_En_7_Chapter.xhtml), we’ll look at unsupervised machine learning. I’ll begin by providing an overview of the different categories of supervised machine learning. In the second section, I will cover various regression methods, and we will build machine learning models, using PySpark’s MLlib library. The third and final section of this chapter focuses on classification, using multiple machine learning algorithms.

## Supervised Machine Learning Primer

In supervised machine learning, as the name suggests, the learning process is supervised, as the machine learning algorithm being used corrects its predictions, based on the actual output. In supervised machine learning, the correct labels or output is already known during the model training phase, and, hence, the error can be reduced accordingly. In short, we try to map the relationship between the input data and output label in such a way as to pick up the signals from the training data and generalize about the unseen data as well. The training of the model consists of comparing the actual output with the predicted output and then making the changes in predictions, to reduce the total error between what is actual and what is predicted. The supervised machine learning process followed is as shown in Figure [6-1](#478390_1_En_6_Chapter.xhtml#Fig1).

![../images/478390_1_En_6_Chapter/478390_1_En_6_Fig1_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Fig1_HTML.jpg)

Figure 6-1

Supervised learning approach

The data used for training the model is preprocessed, and features are created accordingly. Once the machine learning model is trained, it can be used to make predictions on the unseen data. So, in the preceding figure, we can see how the model is trained, using input data and how now, the trained model is used to predict whether the new transaction is genuine. This type of learning is predominantly used in cases in which historical data is available and predictions must be made on future data. The further categorization of supervised learning is based on types of output or target variables being used for prediction:

*   Regression

*   Classification

Regression is used when the target value that is being predicted is continuous or numerical in nature. For example, predicting salary based on a given number of years of experience or education falls under the category of regression.

### Note

Although there are multiple types of regression, in this chapter, I’ll focus on linear regression and some of its associated algorithms, as you’ll see shortly.

Classification is used if the target variable is a discrete value or categorical in nature. For example, predicting whether a customer will churn out is a type of classification problem, as shown in Figure [6-2](#478390_1_En_6_Chapter.xhtml#Fig2).

![../images/478390_1_En_6_Chapter/478390_1_En_6_Fig2_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Fig2_HTML.jpg)

Figure 6-2

Types of suprvised tasks

Classification tasks can further be broken down into two categories: binary class and multi-class, as shown in Figure [6-3](#478390_1_En_6_Chapter.xhtml#Fig3).

![../images/478390_1_En_6_Chapter/478390_1_En_6_Fig3_HTML.png](images/478390_1_En_6_Chapter/478390_1_En_6_Fig3_HTML.png)

Figure 6-3

Types of classes

### Binary Classification

When the target or output variable contains only up to two categories, it is referred to as binary classification. So, every record in the data can only fall under one of the two groups. For example:

*   Yes or no

*   Group A or group B

*   Sell or not sell

*   Positive or negative

*   Accepted or rejected

### Multi-class Classification

When the target or output variable contains more than two categories, it is referred to as multi-class classification. So, there can be multiple groups within the data, and every record can belong to any of the groups. For example:

*   Yes or no or maybe

*   Group A or group B or group C

*   Category 1 or category 2 or category 3 or others

*   Rank 1 or rank 2 or rank 3 or rank 4 or rank 5

Another useful property of supervised learning is that the model’s performance can be evaluated on training and test data. Based on the type of model (classification or regression), the evaluation metric can be applied, and performance results can be measured. In this chapter, I will cover how to build machine learning models to execute regression and binary classification.

## Building a Linear Regression Model

Linear regression refers to modeling the relationship between a set of independent variables and the output or dependent (numerical) variables. If the input variables include more than one variable, this is known as multivariable linear regression. In short, it is assumed that the dependent variable is a linear combination of other independent variables.

![$$ \overline{y}={B}_0+{B}_1\ast {X}_1+{B}_2\ast {X}_{2+\dots } $$](images/478390_1_En_6_Chapter/478390_1_En_6_Chapter_TeX_Equa.png)

Here *X*[1], *X*[2], … are the independent variables that are used to predict the output variable. The output of the linear regression is a straight line, which minimizes the actual vs. predicted values. A linear regression model cannot handle nonlinear data, as it’s only possible to model linearly separable data, therefore polynomial regression is used for nonlinear data, and the output is generally a curve, instead of a straight line, as shown in Figure [6-4](#478390_1_En_6_Chapter.xhtml#Fig4).

![../images/478390_1_En_6_Chapter/478390_1_En_6_Fig4_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Fig4_HTML.jpg)

Figure 6-4

Types of regression

Linear regression also assumes that data is normally distributed, in order to improve prediction. Linear regression is one of the ways to predict continuous values, and you will see now how we can use other alternatives, to predict numerical output.

The following sections focus on solving regression tasks, using multiple machine learning algorithms. I will begin with data ingestion and exploratory data analysis and then build models. The steps 1 to 4 will be the same for all the regression models.

### Note

Complete datasets, along with the relevant code, are available for reference from the GitHub repository for this book and execute best on Spark 2.3 and higher versions.

Let’s build a linear regression model, using Spark’s MLlib library, and predict the target variable, using the input features.

### Reviewing the Data Information

The dataset that we are going to use for this example is a sample dataset and contains a total of 1,232 rows and 6 columns. We have to use 5 input variables to predict the target variable, using the linear regression model.

#### Step 1: Create the Spark Session Object

We start the Jupyter notebook, import `SparkSession`, and create a new `SparkSession` object to use with Spark.

```
[In]: from pyspark.sql import SparkSession
[In]: spark=SparkSession.builder.appName('supervised_ml').getOrCreate()
```

#### Step 2: Read the Dataset

We then load and read the dataset within Spark, using DataFrame. We have to make sure that we have opened PySpark from the same directory folder where the dataset is available, or else we have to mention the directory path of the data folder.

```
[In]: df=spark.read.csv('Linear_regression_dataset.csv',inferSchema=True,header=True)
[In]:print((df.count(), len(df.columns)))
[Out]: (1232, 6)
```

The preceding output confirms the size of our dataset, so we can then validate the datatypes of the input values, to check if we have to change/cast any column datatypes. In this example, all columns contain `integer` or `double` values that are already aligned with our requirements.

```
[In]: df.printSchema()
[Out]: root
|-- var_1: integer (nullable = true)
|-- var_2: integer (nullable = true)
|-- var_3: integer (nullable = true)
|-- var_4: double (nullable = true)
|-- var_5: double (nullable = true)
|-- output: double (nullable = true)
```

There is a total of six columns, of which five are input columns (`var_1` to `var_5`) and target columns (label). We can now use the `describe` function to go over statistical measures of the dataset.

```
[In]: df.show(10)
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figa_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figa_HTML.jpg)

#### Step 3: Feature Engineering

This is the part where we create a single vector combining all input features, by using Spark’s `VectorAssembler`. It creates only a single feature that captures the input values for that particular row. So, instead of five input columns, the engine essentially translates the features into a single column with five input values, in the form of a list.

```
[In]: from pyspark.ml.linalg import Vector
[In]: from pyspark.ml.feature import VectorAssembler
```

We will pass all five input columns, to create a single features column.

```
[In]: df.columns
[Out]: ['var_1', 'var_2', 'var_3', 'var_4', 'var_5', 'label']
[In]: vec_assmebler=VectorAssembler(inputCols=['var_1', 'var_2', 'var_3', 'var_4', 'var_5'],outputCol='features')
[In]: features_df=vec_assmebler.transform(df)
[In]: features_df.printSchema()
[Out]: root
|-- var_1: integer (nullable = true)
|-- var_2: integer (nullable = true)
|-- var_3: integer (nullable = true)
|-- var_4: double (nullable = true)
|-- var_5: double (nullable = true)
|-- label: double (nullable = true)
|-- features: vector (nullable = true)
```

As you can see, we have an additional column (features), which contains the single dense vector for all of the inputs. We then take a subset of the dataframe and select only the features column and the label column, to build the linear regression model.

```
[In]: df.select(['features','label']).show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figb_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figb_HTML.jpg)

#### Step 4: Split the Dataset

Let’s split the dataset into training and test datasets, in order to train and evaluate the performance of the linear regression model. We split it according to a 70/30 ratio and train our model on 70% of the dataset. We can print the shape of the train and test data, to validate the size.

```
[In]: train, test = df.randomSplit([0.75, 0.25])
[In]:print(f"Size of train Dataset : {train.count()}" )
[Out]: Size of train Dataset : 911
[In]: print(f"Size of test Dataset : {test.count()}" )
[Out]:  Size of test Dataset : 321
```

#### Step 5: Build and Train Linear Regression Model

Now we build and train the linear regression model, using features, input, and label columns. We first import the linear regression from MLlib, as follows:

```
[In]: from pyspark.ml.regression import LinearRegression
[In]: lr = LinearRegression()
```

### Note

For simplicity, all the machine learning models built in this chapter use default hyperparameters. Readers can use their own set of hyperparameters.

```
[In]:lr_model = lr.fit(train)
[In]: predictions_df=lr_model.transform(test)
[In]: predictions_df.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figc_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figc_HTML.jpg)

#### Step 6: Evaluate Linear Regression Model on Test Data

To check the performance of the model on unseen or test data, we make use of `evaluate`.

```
[In]: model_predictions=lr_model.evaluate(test)
[In]: model_predictions.r2
[Out]: 0.8855561089304634
[In]: print(model_predictions.meanSquaredError)
[Out]:0.00013305453514672318
```

### Generalized Linear Model Regression

The generalized linear model (GLM) is an advanced version of linear regression that considers the target variable to have an error distribution other than a preferred normal distribution. The GLM generalizes linear regression, using a link function, so that variance is a function of the predicted value itself. Let’s try to build the GLM on the same dataset and see if it performs better than a simple linear regression model. First, we must import the GLM from MLlib.

```
[In]: from pyspark.ml.regression import GeneralizedLinearRegression
```

#### Step 1: Build and Train Generalized Linear Regression Model

```
[In]: glr = GeneralizedLinearRegression()
[In]: glr_model = glr.fit(train)
[In]: glr_model.coefficients
[Out]: DenseVector([0.0003, 0.0001, 0.0001, -0.6374, 0.4822])
```

We can get the coefficient values, using coefficient functions of that model. Here we can see that one of the features has a negative coefficient value. We can get more information about the GLM model, by using the `summary` function. It returns all the details, such as coefficient value, std error, AIC (Akaike information criterion) value, and p value.

```
[In]: glr_model.summary
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figd_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figd_HTML.jpg)

#### Step 2: Evaluate the Model Performance on Test Data

```
[In]: model_predictions=glr_model.evaluate(test)
[In]: model_predictions.predictions.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Fige_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Fige_HTML.jpg)

The Akaike information criterion (AIC) is an evaluation parameter of relative performance of quality of models for the same set dataset. AIC is mainly used to select among multiple models for a given dataset. A lesser value of AIC indicates that the model is of good quality. AIC tries to strike a balance between the variance and bias of the model. Therefore, it deals with the chances both of overfitting and underfitting. The model with the lowest AIC score is preferred over other models.

```
[In]: model_predictions.aic
[Out]: -1939.88
```

We can run the GLM for multiple distributions, such as

1.  Binomial

2.  Poisson

3.  Gamma

4.  Tweedie

```
[In]: glr = GeneralizedLinearRegression(family='Binomial')
[In]: glr_model = glr.fit(train)
[In]: model_predictions=glr_model.evaluate(test)
[In]: model_predictions.aic
[Out]: 336.991
[In]: glr = GeneralizedLinearRegression(family='Poisson')
[In]: glr_model = glr.fit(train)
[In]: predictions=glr_model.evaluate(test)
[In]: predictions.aic
[Out]: 266.53
[In]: glr = GeneralizedLinearRegression(family='Gamma')
[In]: glr_model = glr.fit(train)
[In]:model_predictions=glr_model.evaluate(test)
[In]: model_predictions.aic
[Out]: -1903.81
```

Here we can see that our default GLM model with Gaussian distribution has the lowest AIC value, compared to others.

### Decision Tree Regression

The decision tree regression algorithm can be used for both regression and classification. It is quite powerful in terms of fitting the data well but comes with the high risk of sometimes overfitting the data. Decision trees contain multiple splits based on entropy or Gini indexes. The deeper the tree, the higher the chance of overfitting the data. In our example, we will build a decision tree for predicting the target value, with the default value of parameters (`maxdepth = 5`).

#### Step 1: Build and Train Decision Tree Regressor Model

```
[In]: from pyspark.ml.regression import DecisionTreeRegressor
[In]: dec_tree = DecisionTreeRegressor()
[In]: dec_tree_model = dec_tree.fit(train)
[In]: dec_tree_model.featureImportances
[Out]: SparseVector(5, {0: 0.9641, 1: 0.0193, 2: 0.0029, 3: 0.0053, 4: 0.0084})
```

#### Step 2: Evaluate the Model Performance on Test Data

```
[In]: model_predictions = dec_tree_model.transform(test)
[In]: model_predictions.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figf_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figf_HTML.jpg)

We import `RegressionEvaluation` from MLlib, to evaluate the performance of the decision tree on test data. As of now, there are two metrics available for evaluation: *r*² and RMSE (root mean squared error). *r*² mainly suggests how much of the variation in the dataset can be attributed to regression. Therefore, the higher the *r*², the better the performance of the model. On the other hand, RMSE suggests the total errors the model is making, in terms of the difference between actual and predicted values.

```
[In]: from pyspark.ml.evaluation import RegressionEvaluator
[In]: dt_evaluator = RegressionEvaluator(metricName='r2')
[In]: dt_r2 = dt_evaluator.evaluate(model_predictions)
[In]: print(f'The r-square value of DecisionTreeRegressor is {dt_r2}')
[Out]: The r-square value of DecisionTreeRegressor is 0.8093834699203476
[In]: dt_evaluator = RegressionEvaluator(metricName='rmse')
[In]: dt_rmse = dt_evaluator.evaluate(model_predictions)
[In]: print(f'The rmse value of DecisionTreeRegressor is {dt_rmse}')
[Out]: The rmse value of DecisionTreeRegressor is 0.014111932287681688
```

The *r*² value of this particular model is close to 0.81, which is a little lower than that of a simple linear regression model.

### Random Forest Regressors

Random forest regressors are a collection of multiple individual decision trees built using different samples of data. The whole idea of combining these individual trees is to take majority voting or averages (in case of regression) to generalize effectively. A random forest is, therefore, an ensembling technique that takes a bagging approach. It can be used for regression as well as for classification tasks. Because decision trees tend to overfit the data, random forests remove the element of high variance, by taking the means of the predicted values from individual trees. In our example, we will build a random forest model for regression, using default parameters (`numTrees = 20`)

#### Step 1: Build and Train Random Forest Regressor Model

```
[In]: from pyspark.ml.regression import RandomForestRegressor
[In]: rf = RandomForestRegressor()
[In]: rf_model = rf.fit(train)
[In]:  rf_model.featureImportances
[Out]: SparseVector(5, {0: 0.4395, 1: 0.045, 2: 0.0243, 3: 0.2725, 4: 0.2188})
```

As you can see, the number of trees in the random forest is equal to 20. This number can be increased.

```
[In]: rf_model.getNumTrees
[Out]: 20
[In]: model_predictions = rf_model.transform(test)
[In]: model_predictions.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figg_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figg_HTML.jpg)

#### Step 2: Evaluate the Model Performance on Test Data

We can again use *r*²and RMSE as the evaluation parameter of the random forest model.

```
[In]:rf_evaluator = RegressionEvaluator(metricName='r2')
[In]: rf_r2 = rf_evaluator.evaluate(model_predictions)
[In]: print(f'The r-square value of RandomForestRegressor is {rf_r2}')
[Out]: The r-square value of RandomForestRegressor is 0.8215863293044671
[In]: rf_evaluator = RegressionEvaluator(metricName='rmse')
[In]: rf_rmse = rf_evaluator.evaluate(model_predictions)
[In]: print(f'The rmse value of RandomForestRegressor is {rf_rmse}')
[Out]: The rmse value of RandomForestRegressor is 0.01365275410722947
```

As you can see, it clearly outperforms the decision tree regressor and has a higher r². The performance of this model can further be enhanced with hyperparameter tuning.

## Gradient-Boosted Tree Regressor

A gradient-boosted tree (GBT) regressor is also an ensembling technique, which uses boosting under the hood. Boosting refers to making use of individual weak learners in order to boost the performance of the overall model. One major difference between bagging and boosting is that in bagging, the individual models that are built are parallel in nature, meaning they can be built independent of each other, but in boosting, the individual models are built in a sequential manner. In a gradient boosting approach, the second model focuses on the errors made by the first model and tries to reduce overall errors for those data points. Similarly, the next model tries to reduce the errors made by the previous model. In this way, the overall error of prediction is reduced. In the following example, we will build a GBT regressor with default parameters.

### Step 1: Build and Train a GBT Regressor Model

```
[In]: from pyspark.ml.regression import GBTRegressor
[In]: gbt = GBTRegressor()
[In]: gbt_model=gbt.fit(train)
[In]: gbt_model.featureImportances
[Out]: SparseVector(5, {0: 0.2325, 1: 0.2011, 2: 0.1645, 3: 0.2268, 4: 0.1751})
[In]: model_predictions = gbt_model.transform(test)
[In]: model_predictions.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figh_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figh_HTML.jpg)

### Step 2: Evaluate the Model Performance on Test Data

```
[In]: gbt_evaluator = RegressionEvaluator(metricName='r2')
[In]: gbt_r2 = gbt_evaluator.evaluate(model_predictions)
[In]: print(f'The r-square value of GradientBoostedRegressor is {gbt_r2}')
[Out]: The r-square value of GradientBoostedRegressor is 0.8477273892307596
[In]: gbt_evaluator = RegressionEvaluator(metricName='rmse')
[In]: gbt_rmse = gbt_evaluator.evaluate(model_predictions)
[In]: print(f'The rmse value of GradientBoostedRegressor is {gbt_rmse}')
[Out]: The rmse value of GradientBoostedRegressor is 0.013305445803592103
```

As you can see, the GBT regressor outperforms the random forestmodel. With r² being close to 0.85, it can be considered the final model, after proper tuning.

## Building Multiple Models for Binary Classification Tasks

In this third and final section of the chapter, you will see how to build multiple machine learning models for binary classification tasks. The data that we are going to use for this is a subset of an open source Bank Marketing Data Set from the UCI ML repository, available at `https://archive.ics.uci.edu/ml/datasets/Bank+Marketing`.

There are two reasons for selecting only a subset of this data. The first is to maintain the class balance for the classification task, so as not to make it an anomalous detection category task. Another reason for selecting only a subset of the features is to limit the amount of signals in the data, as some of the features in the dataset strongly affect the output and, therefore, are ignored in this exercise.

The dataset contains 9,500 rows and 8 columns. The idea is to predict if the user will subscribe to another product or service (term deposit), based on the other attributes, such as age, job, loan, etc. This is a typical requirement in which machine learning is leveraged to find the top users who can be targeted by the business for cross-selling or upselling.

I’ll begin with the logistic regression model.

### Logistic Regression

Logistic regression is considered to be one of baseline models, owing to its simplicity and interpretability. Under the hood, it is quite similar to linear regression. It also assumes that output is a linear combination of the dependent variables, but to keep the output between 0 and 1, as it returns the probability as output, it makes use of a nonlinear function (`sigmoid`), which produces an S curve instead of a straight line (linear regression).

We’ll start by building the baseline in Steps 1–3 and then complete the logistic regression model with default hyperparameters, in Steps 4–6.

#### Step 1: Read the Dataset

```
[In]: df=spark.read.csv('bank_data.csv',inferSchema=True,header=True)
[In]: df.count()
[Out]: 9501
[In]: df.columns
[Out]: ['age', 'job', 'marital', 'education', 'default', 'housing','loan', 'target_class']
[In]: df.printSchema()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figi_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figi_HTML.jpg)

As you can see, the input columns are all the columns, except for the target class column. The target class is also well-balanced, in terms of the count of yes and no labels. We will have to convert yeses and noes into 1s and 0s, as well as rename the `target_class` column to “label,” which is the default acceptance column name in machine learning model parameters.

```
[In]: df.groupBy('target_class').count().show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figj_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figj_HTML.jpg)

#### Step 2: Feature Engineering for Model

```
[In]: from pyspark.sql import functions as F
[In]: from pyspark.sql import ∗
[In]: df=df.withColumn("label", F.when(df.target_class =='no', F.lit(0)).otherwise(F.lit(1)))
[In]: df.groupBy('label').count().show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figk_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figk_HTML.jpg)

Now that we have renamed the output column “label” and converted the target class to 1s and 0s, the next step is to create features for the model. Because we have categorical columns, such as job and edu, we will have to use `StringIndexer` and `OneHotEncoder` to convert them into a numerical format. We create a Python function, `cat_to_num`, to convert all the categorical features into numerical ones.

```
[In]: from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler
[In]: def cat_to_num(df):
for col in df.columns:
stringIndexer = StringIndexer(inputCol=col, outputCol=col+"_index")
model = stringIndexer.fit(df)
indexed = model.transform(df)
encoder = OneHotEncoder(inputCol=col+"_index", outputCol=col+"_vec")
df = encoder.transform(indexed)
df_assembler = VectorAssembler(inputCols=['age','marital_vec','education_vec','default_vec','housing_vec','loan_vec'], outputCol="features")
df = df_assembler.transform(df)
return df.select(['features','label'])
```

We just select the new features column and target label column, as we don’t need the earlier original columns for model training.

```
[In]: df_new=cat_to_num(df)
[In]: df_new.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figl_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figl_HTML.jpg)

Now we have all the input features merged into a single dense vector (`'features'`), along with output column labels, which we can use to train the machine learning models. The new dataframe created using only two columns (features, label) is now called `df_new` and will be used for every model. We can now split this new dataframe into train and test datasets. We can split the data into a 75%/25% ratio, using the `randomplit` function.

#### Step 3: Split the Data into Train and Test Datasets

```
[In]: train, test = df_new.randomSplit([0.75, 0.25])
[In]: print(f"Size of train Dataset : {train.count()}" )
[Out]: 7121
[In]: print(f"Size of test Dataset : {test.count()}" )
[Out]: 2380
```

#### Step 4: Build and Train the Logistic Regression Model

```
[In]: from pyspark.ml.classification import LogisticRegression
[In]: lr = LogisticRegression()
[In]: lr_model = lr.fit(train)
[In]:print( lr_model.coefficients)
[Out]:
[0.0272019114172,-0.647672064875,0.229030508111,-0.77074788287,-12.36869511,-12.8865599132,-13.2257790609,-12.6705131313,-13.0023164274,-13.0747662586,-12.6985757761,1.42220523957,0.301582233094,-0.0127231892838,0.218471149577,0.332362933568]
```

Once the model is built, we can make use of the internal function `summary`, which offers important details regarding the model, such as ROC curve, precision, recall, AUC (area under the curve), etc.

#### Step 5: Evaluate Performance on Training Data

```
[In]: lr_summary=lr_model.summary
[In]: lr_summary.accuracy
[Out]: 0.673079623648364
[In]: lr_summary.areaUnderROC
[Out]: 0.7186044694483512
[In]: lr_summary.weightedRecall
[Out]: 0.673079623648364
[In]: lr_summary.weightedPrecision
[Out]: 0.6750967624018298
```

Here, using the `summary` function , we can view the model’s performance on train data, such as its accuracy, AUC, weighted recall, and precision. We can also view additional details—such as how precision varies for various threshold values, the relation between precision and recall, and how recall varies with different threshold values—to pick the right threshold value for the model. These also can be plotted, to view the relationships.

```
[In]: lr_summary.precisionByThreshold.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figm_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figm_HTML.jpg)

```
[In]: lr_summary.roc.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Fign_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Fign_HTML.jpg)

```
[In]: lr_summary.recallByThreshold.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figo_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figo_HTML.jpg)

```
[In]: lr_summary.pr.show()
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figp_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figp_HTML.jpg)

#### Step 6: Evaluate Performance on Test Data

```
[In]: model_predictions = lr_model.transform(test)
[In]: model_predictions.columns
[Out]: ['features', 'label', 'rawPrediction', 'probability', 'prediction']
[In]: model_predictions.select(['label','probability','prediction']).show(10,False)
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figq_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figq_HTML.jpg)

As you can see, the prediction column shows the model prediction for each of the records in the test data. The probability column shows the values for both classes (0 & 1). The probability at 0th index is of 0; the other is for a prediction of 1\. The evaluation of the logistic regression model on test data can be done using `BinaryClassEvaluator` . We can get the area under ROC and that under the PR curve, as shown following:

```
[In]:from pyspark.ml.evaluation import BinaryClassificationEvaluator
[In]:lr_evaluator = BinaryClassificationEvaluator(metricName='areaUnderROC')
[In]: lr_auroc = lr_evaluator.evaluate(model_predictions)
[In]: print(f'The auroc value of Logistic Regression Model is {lr_auroc}')
[Out]: The auroc value of Logistic Regression Model is 0.7092938229110143
[In]: lr_evaluator = BinaryClassificationEvaluator(metricName='areaUnderPR')
[In]: lr_aupr = lr_evaluator.evaluate(model_predictions)
[In]: print(f'The aupr value of Logistic Regression Model is {lr_aupr}')
[Out]: The aupr value of Logistic Regression Model is 0.6630743130940658
[In]: true_pos=model_predictions.filter(model_predictions['label']==1).filter(model_predictions['prediction']==1).count()
[In]: actual_pos=model_predictions.filter(model_predictions['label']==1).count()
[In]: pred_pos=model_predictions.filter(model_predictions['prediction']==1).count()
Recall
[In]: float(true_pos)/(actual_pos)
[Out]: 0.6701030927835051
Precision
[In]: float(true_pos)/(pred_pos)
[Out]: 0.6478405315614618
```

### Decision Tree Classifier

As mentioned earlier, decision trees can be used for classification as well as regression. Here, we will build a decision tree with default hyperparameters and use it to predict whether the user will opt for the new term deposit plan.

#### Step 1: Build and Train Decision Tree Classifier Model

```
[In]: from pyspark.ml.classification import DecisionTreeClassifier
[In]: dt = DecisionTreeClassifier()
[In]: dt_model = dt.fit(train)
[In]: model_predictions = dt_model.transform(test)
[Out]: model_predictions.select(['label','probability','prediction']).show(10,False)
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figr_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figr_HTML.jpg)

#### Step 2: Evaluate Performance on Test Data

```
[In]: dt_evaluator = BinaryClassificationEvaluator(metricName='areaUnderROC')
[In]: dt_auroc = dt_evaluator.evaluate(model_predictions)
[In]: print(f'The auc value of Decision Tree Classifier Model is {dt_auroc}')
[Out]: The auc value of Decision Tree Classifier Model is 0.516199386190993
[In]: dt_evaluator = BinaryClassificationEvaluator(metricName='areaUnderPR')
[In]: dt_aupr = dt_evaluator.evaluate(model_predictions)
[In]: print(f'The aupr value of Decision Tree Model is {dt_aupr}')
[Out]: The aupr value of Decision Tree Model is 0.46771834172588167
[In]: true_pos=model_predictions.filter(model_predictions['label']==1).filter(model_predictions['prediction']==1).count()
[In]: actual_pos=model_predictions.filter(model_predictions['label']==1).count()
[In]: pred_pos=model_predictions.filter(model_predictions['prediction']==1).count()
[In]: float(true_pos)/(actual_pos)
[Out]: 0.6907216494845361
[In]: float(true_pos)/(pred_pos)
[Out]: 0.6661143330571665
```

### Support Vector Machines Classifiers

Support vector machines (SVMs) are used for classification tasks, as they find the hyperplane that maximizes the margin (perpendicular distance) between two classes. All the instances and target classes are represented as vectors in high-dimensional space, and the SVM finds the closest two points from the two classes that support the best separating line or hyperplane, as shown in Figure [6-5](#478390_1_En_6_Chapter.xhtml#Fig5).

![../images/478390_1_En_6_Chapter/478390_1_En_6_Fig5_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Fig5_HTML.jpg)

Figure 6-5

Support vector machine

For nonlinearly separable data, there are different kernel tricks to separate the classes. In our example, we will build a linearly separable support vector classifier with default hyperparameters.

#### Step 1: Build and Train SVM Model

```
[In]: from pyspark.ml.classification import LinearSVC
[In]: lsvc = LinearSVC()
[In]: lsvc_model = lsvc.fit(train)
[In]: model_predictions = lsvc_model.transform(test)
[In]: model_predictions.columns
[Out]: ['features', 'label', 'rawPrediction', 'prediction']
[In]:model_predictions.select(['label','prediction']).show(10,False)
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figs_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figs_HTML.jpg)

#### Step 2: Evaluate Performance on Test Data

```
[In]: svc_evaluator = BinaryClassificationEvaluator(metricName='areaUnderROC')
[In]: svc_auroc = svc_evaluator.evaluate(model_predictions)
[In]: print(f'The auc value of SupportVectorClassifier  is {svc_auroc}')
[Out]: The auc value of SupportVectorClassifier  is 0.7043772749366973
[In]: svc_evaluator = BinaryClassificationEvaluator(metricName='areaUnderPR')
[In]: svc_aupr =svc_evaluator.evaluate(model_predictions)
[In]: print(f'The aupr value of SupportVectorClassifier Model is {svc_aupr}')
[Out]: The aupr value of SupportVectorClassifier Model is 0.6567277377856992
[In]: true_pos=model_predictions.filter(model_predictions['label']==1).filter(model_predictions['prediction']==1).count()
[In]: actual_pos=model_predictions.filter(model_predictions['label']==1).count()
[In]: pred_pos=model_predictions.filter(model_predictions['prediction']==1).count()
[In]: float(true_pos)/(actual_pos)
[Out]: 0.7774914089347079
[In]: float(true_pos)/(pred_pos)
[Out]: 0.600132625994695
```

### Naive Bayes Classifier

Naive Bayes (NB) classifiers work on the principle of conditional probability and assume absolute independence between predictors. An NB classifier doesn’t have many hyperparameters and can outperform some of the most sophisticated algorithms out there. In the following example, we will build an NB classifier and evaluate its performance on the test data.

#### Step 1: Build and Train SVM Model

```
[In]: from pyspark.ml.classification import NaiveBayes
[In]: nb = NaiveBayes()
[In]: nb_model = nb.fit(train)
[In]: model_predictions = nb_model.transform(test)
[In]: model_predictions.select(['label','probability','prediction']).show(10,False)
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figt_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figt_HTML.jpg)

#### Step 2: Evaluate Performance on Test Data

```
[In]: nb_evaluator = BinaryClassificationEvaluator(metricName='areaUnderROC')
[In]: nb_auroc = nb_evaluator.evaluate(model_predictions)
[In]: print(f'The auc value of NB Classifier is {nb_auroc}')
[Out]: The auc value of NB Classifier is 0.43543736717760884
[In]: nb_evaluator = BinaryClassificationEvaluator(metricName='areaUnderPR')
[In]: nb_aupr =nb_evaluator.evaluate(model_predictions)
[In]: print(f'The aupr value of NB Classifier Model is {nb_aupr}')
[Out]: The aupr value of NB Classifier Model is 0.4321001351769349
[In]: true_pos=model_predictions.filter(model_predictions['label']==1).filter(model_predictions['prediction']==1).count()
[In]: actual_pos=model_predictions.filter(model_predictions['label']==1).count()
[In]: pred_pos=model_predictions.filter(model_predictions['prediction']==1).count()
[In]: float(true_pos)/(actual_pos)
[Out]: 0.586
[In]: float(true_pos)/(pred_pos)
[Out]: 0.625
```

### Gradient Boosted Tree Classifier

So far, we have used single algorithms for classification. Now we move on to use ensemble methods, such as GBT and random forests, for classification. Bagging and boosting for classification works according to similar principles as regression.

#### Step 1: Build and Train the GBT Model

```
[In]: from pyspark.ml.classification import GBTClassifier
[In]: gbt = GBTClassifier()
[In]: gbt_model = gbt.fit(train)
[In]: model_predictions = gbt_model.transform(test)
[In]: model_predictions.select(['label','probability','prediction']).show(10,False)
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figu_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figu_HTML.jpg)

#### Step 2: Evaluate Performance on Test Data

```
[In]: gbt_evaluator = BinaryClassificationEvaluator(metricName='areaUnderROC')
[In]: gbt_auroc = gbt_evaluator.evaluate(model_predictions)
[In]: print(f'The auc value of GradientBoostedTreesClassifier is {gbt_auroc}')
[Out]: The auc value of GradientBoostedTreesClassifier is 0.7392410330756018
[In]: gbt_evaluator = BinaryClassificationEvaluator(metricName='areaUnderPR')
[In]: gbt_aupr = gbt_evaluator.evaluate(model_predictions)
[In]: print(f'The aupr value of GradientBoostedTreesClassifier Model is {gbt_aupr}')
[Out]: The aupr value of GradientBoostedTreesClassifier Model is 0.7345982892755392
[In]: true_pos=model_predictions.filter(model_predictions['label']==1).filter(model_predictions['prediction']==1).count()
[In]: actual_pos=model_predictions.filter(model_predictions['label']==1).count()
[In]: pred_pos=model_predictions.filter(model_predictions['prediction']==1).count()
[In]: float(true_pos)/(actual_pos)
[Out]: 0.668
[In]: float(true_pos)/(pred_pos)
[Out]: 0.674
```

### Random Forest Classifier

Once again, a random forest classifier is a collection of multiple decision tree classifiers. It works on the voting mechanism and predicts the output class that received the maximum votes from all individual decision trees. Let’s build a random forest classifier with the same data.

#### Step 1: Build and Train the Random Forest Model

```
[In]: from pyspark.ml.classification import RandomForestClassifier
[In]: rf = RandomForestClassifier(numTrees=50,maxDepth=30)
[In]: rf_model = rf.fit(train)
[In]: model_predictions=rf_model.transform(test)
[In]: model_predictions.select(['label','probability','prediction']).show(10,False)
[Out]:
```

![../images/478390_1_En_6_Chapter/478390_1_En_6_Figv_HTML.jpg](images/478390_1_En_6_Chapter/478390_1_En_6_Figv_HTML.jpg)

#### Step 2: Evaluate Performance on Test Data

```
[In]: rf_evaluator = BinaryClassificationEvaluator(metricName='areaUnderROC')
[In]: rf_auroc = rf_evaluator.evaluate(model_predictions)
[In]: print(f'The auc value of RandomForestClassifier Model is {rf_auroc}')
[Out]:
The auc value of RandomForestClassifier Model is 0.7326433634020617
[In]: rf_evaluator = BinaryClassificationEvaluator(metricName='areaUnderPR')
[In]: rf_aupr = rf_evaluator.evaluate(model_predictions)
[In]: print(f'The aupr value of RandomForestClassifier Model is {rf_aupr}')
[Out]: The aupr value of RandomForestClassifier Model is 0.7277253895494864
[In]; true_pos=model_predictions.filter(model_predictions['label']==1).filter(model_predictions['prediction']==1).count()
[In]: actual_pos=model_predictions.filter(model_predictions['label']==1).count()
[In]: pred_pos=model_predictions.filter(model_predictions['prediction']==1).count()
[In]: float(true_pos)/(actual_pos)
[Out]: 0.67
[In]: float(true_pos)/(pred_pos)
[Out]: 0.67
```

So far, we have been using the default hyperparameters for all the models, but it’s rarely the case that the model can perform to the best of its ability with those default settings. Therefore, it’s imperative that we tune the models for the right combination of hyperparameters.

## Hyperparameter Tuning and Cross-Validation

In the following example, we will take the random forest model that we just built and try to find the best combination of its hyperparameters, to improve performance. We can use `ParamgridBuilder` and `CrossValidator` for hyperparameter tuning. We will pass different values in the parameter grid for three hyperparameters (`maxDepth`, `maxBins`, and `numTrees`). It might take a few minutes to complete, as it builds random forest models for all of these combinations before returning the best possible combination for this train dataset.

```
[In]: from pyspark.ml.tuning import ParamGridBuilder, CrossValidator
[In]: rf = RandomForestClassifier()
[In]: paramGrid = (ParamGridBuilder()
.addGrid(rf.maxDepth, [5,10,20,25,30])
.addGrid(rf.maxBins, [20, 60])
.addGrid(rf.numTrees, [5, 20,50,100])
.build())
[In]: cv = CrossValidator(estimator=rf, estimatorParamMaps=paramGrid, evaluator=rf_evaluator, numFolds=5)
[In]: cv_model = cv.fit(train)
[In]: best_rf_model = cv_model.bestModel
best_rf_model contains the best hyper-parameters to be used for training the model on this dataset.
[In]: model_predictions = best_rf_model.transform(test)
[In]: rf_evaluator = BinaryClassificationEvaluator(metricName='areaUnderROC')
[In]: rf_auroc = rf_evaluator.evaluate(model_predictions)
[In]: print(rf_auroc)
[Out]:  0.7425990374615659
```

As you can see, by using the best hyperparameters for our random forest model, the AUC score has increased.

## Conclusion

This chapter covered in detail the different types of supervised learning and ways to solve binary classification with multiple machine learning algorithms. How to choose the best hyperparameters for a model and using cross-validation techniques to build the best possible model on the given dataset were also explained.

# 7. Unsupervised Machine Learning

As the name suggests, unsupervised machine learning does not include finding relationships between input and output. To be honest, there is no output that we try to predict in unsupervised learning. It is mainly used to group together the features that seem to be similar to one another in some sense. These can be the distance between those features or some sort of similarity metric. In this chapter, I will touch on some unsupervised machine learning techniques and build one of the machine learning models, using PySpark to categorize users into groups and, later, to visualize those groups as well.

## Unsupervised Machine Learning Primer

As suggested, unsupervised learning does not aim to map the relationship between input and output; rather, it tries to group values that are similar to one another. There is no training that takes place on the input data. Rather, it does this by finding the underlying signals and patterns in the data, to form groups within. Unsupervised learning can be categorized further into two separate categories:

1.  Clustering

2.  Association rules

Clustering refers to finding underlying groups of data points, based on the attributes present in the data, as shown in Figure [7-1](#478390_1_En_7_Chapter.xhtml#Fig1).

![../images/478390_1_En_7_Chapter/478390_1_En_7_Fig1_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Fig1_HTML.jpg)

Figure 7-1

Unsupervised machine learning

Association signifies the probability of co-occurrence. For example, if someone bought item X, what’s the probability that he or she might also buy item Y with it. In this chapter, I will focus only on clustering techniques. We can easily make use of clustering to understand the different groups in the data. For example, if we have some data about soccer players, we can easily predict who plays what position on the field—either they are forward players or defenders. Table [7-1](#478390_1_En_7_Chapter.xhtml#Tab1) shows some sample data for players in a soccer tournament. The two values captured are

Table 7-1

Attempts vs. Goals

| Sr. No | No. of Goal Attempts | No. of Tackles |
| --- | --- | --- |
| 1 | 8 | 2 |
| 2 | 2 | 10 |
| 3 | 9 | 1 |
| 4 | 15 | 1 |
| 5 | 2 | 17 |
| 6 | 4 | 6 |
| 7 | 8 | 2 |
| 8 | 0 | 25 |
| 9 | 1 | 17 |
| 10 | 0 | 15 |

1.  Total number of goal attempts

2.  Total number of tackles made

If we try to visualize the preceding values on a scatter plot, we get something similar to what is shown in Figure [7-2](#478390_1_En_7_Chapter.xhtml#Fig2). The x axis indicates the number of tackles made by the players, and the y axis shows the number of attempts to score a goal.

![../images/478390_1_En_7_Chapter/478390_1_En_7_Fig2_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Fig2_HTML.jpg)

Figure 7-2

Scatter plot of attempts vs. goals scored

Up to this point, we don’t know which players are strikers, midfielders, or defenders, but if we use some sort of clustering technique on this data, we get the groups, as shown in Figure [7-3](#478390_1_En_7_Chapter.xhtml#Fig3). The figure clearly shows that there are three groups in this data. The top-left corner of the chart groups all the strikers, and the bottom-right cluster represents the defenders with the highest number of tackles and fewest attempts to score. There is also a player between these two groups who probably belongs to the midfield category, having a reasonable number of tackles and attempts to score a goal.

![../images/478390_1_En_7_Chapter/478390_1_En_7_Fig3_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Fig3_HTML.jpg)

Figure 7-3

Groups within the data

In this example, we see how we can still predict the groups present in the data without any supervised training at all.

Clustering can have multiple applications, including the following:

1.  Anomaly detection

2.  Predictive maintenance

3.  Customer segmentation

There are a number of clustering algorithms that can be used, depending on the available data and specific requirement. In this chapter, I am going to focus on k-means algorithms, to create clusters for sets of users who listen to different genres of music online. K-means is one of the strongest algorithms for this grouping exercise. *K* stands for the number of clusters or groups that must be formed from the data. K-means works by calculating the distance of each data point from the rest and tries to group the nearest ones, until the desired number of K values is reached. We will try to divide users into meaningful groups, using k-means algorithm, so that recommendations can be made according to their tastes in a specific musical genre.

## Reviewing the Dataset

The dataset that we are going to use for this example is a sampled dataset that contains only two columns: the user id and the music category. There are close to half a million records available in this dataset.

### Importing SparkSession and Creating an Object

The first step is to import `SparkSession` and create a Spark object, in order to use PySpark.

```
[In]: from pyspark.sql import SparkSession
[In]: spark=SparkSession.builder.appName('unsupervised_learning').getOrCreate()
```

We will also import several other libraries, such as Pandas and NumPy, for later use.

```
[In]: import pyspark
[In]: import pandas as pd
[In]: import numpy as np
[In]: import matplotlib.pyplot as plt
[In]: from pyspark.sql.functions import *
[In]: from pyspark.sql.types import *
[In]: from pyspark.ml.clustering import KMeans
[In]: df=spark.read.csv('music_data.csv',inferSchema=True,header=True)
[In]: df.count()
[Out]: 429023
[In]: df.printSchema()
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figa_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figa_HTML.jpg)

The preceding confirms that we are dealing with only two columns in the dataset. We can further explore this dataframe by doing a few aggregations. The total number of distinct music categories can be determined with the `distinct` function. The topmost preferred music category among users can be found by sorting the count values grouped under the music category column, as shown following:

```
[In]: df.select('music category').distinct().count()
[Out]: 21
[In]:df.groupBy('music category').count().orderBy('count',ascending=False).show(100,False)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figb_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figb_HTML.jpg)

Alternative music and blues seem to be the genres most preferred by the users. We can also find the total number of unique users in the system in a manner similar to the one preceding.

```
[In]: df.select('user_id').distinct().count()
[Out]: 775
[In]:df.groupBy('user_id').count().orderBy('count',ascending=False).show(20,False)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figc_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figc_HTML.jpg)

```
[In]:df.groupBy('user_id').count().orderBy('count',ascending=True).show(20,False)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figd_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figd_HTML.jpg)

In this dataframe, we have a mix of users. Some have listened to songs as many as 14,000 times, and others have listened only once. The next task is to reshape this dataframe, in order to use clustering on it.

## Reshaping a Dataframe for Clustering

We pivot the data on user ID and music category and fill the values with the total count of songs the user has listened to. We use the `crosstab` function to pivot the data.

```
[In]: feature_df=df.stat.crosstab("user_id", "music category")
[In]: feature_df.printSchema()
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Fige_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Fige_HTML.jpg)

```
[In]: feature_df.show(3,False)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figf_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figf_HTML.jpg)

```
[In]: from pyspark.ml.linalg import Vectors
[In]: from pyspark.ml.feature import VectorAssembler
```

Now that we have the required attributes in the new dataframe, we must assemble them, to create a single feature vector, using `VectorAssembler` . One key thing to remember here is that we don’t use the index of the dataframe that essentially contains the user IDs for `VectorAssembler`.

```
[In]: print(feature_df.columns)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figg_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figg_HTML.jpg)

```
[In]: feat_cols=[col for col in feature_df.columns if col != 'user_id_music category']
[In]: print(feat_cols)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figh_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figh_HTML.jpg)

```
[In]: vec_assembler = VectorAssembler(inputCols = feat_cols, outputCol="features")
[In]: final_data = vec_assembler.transform(feature_df)
[In]: final_data.printSchema()
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figi_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figi_HTML.jpg)

### Note

One thing to remember is that clustering can be sensitive to the scale of the data, for the simple reason that it uses a distance metric to compare the similarity between two values (multidimensionality). Therefore, it’s always a good idea to scale down the data before applying clustering.

Next, we import `StandardScaler` in the Spark library and apply it on the feature vectors.

```
[In]: from pyspark.ml.feature import StandardScaler
[In]:scaler = StandardScaler(inputCol="features", outputCol="scaledFeatures", withStd=True, withMean=False)
[In]: scalerModel = scaler.fit(final_data)
[In]: cluster_final_data.columns
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figj_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figj_HTML.jpg)

The next step is to actually build clusters, using a k-means clustering algorithm.

### Building Clusters with K-Means

One of the key questions to ask before a clustering exercise is: “How many clusters should be formed from the data?” One way to approach this issue is with the Elbow method, by which we try to plot the total sum of squared errors within a cluster against the number of clusters. This gives us a sense of what’s a good number of groups that can be formed from the given data. Basically, we apply k-means for a set consisting of a pre-decided number of clusters (2–10) and visualize the errors against it. Wherever there is an elbow kind of shape forming on the chart, that is a good number to pick for k.

```
[In]: errors=[]
for k in range(2,10):
kmeans = KMeans(featuresCol='scaledFeatures',k=k)
model = kmeans.fit(cluster_final_data)
wssse = model.computeCost(cluster_final_data)
errors.append(wssse)
print("With K={}".format(k))
print("Within Set Sum of Squared Errors = " + str(wssse))
print('--'*30)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figk_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figk_HTML.jpg)

```
[In]: cluster_number = range(2,10)
[In]: plt.scatter(cluster_number,errors)
[In]: plt.xlabel('clusters')
[In]: plt.ylabel('WSSE')
[In]: plt.show()
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Fig4_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Fig4_HTML.jpg)

Figure 7-4

Elbow chart

In our case, there are multiple elbows being formed in the chart. Therefore, we can choose either `4`, `6`, or `8` as the value of k. One thing to remember is that there is no correct answer in clustering. You can have different clusters from the same data, based on techniques being used, as the initiation points differ for every technique. Let’s go ahead and chose the value of k as `6`.

```
[In]: kmeans6 = KMeans(featuresCol='scaledFeatures',k=6)
[In]: model_k6 = kmeans6.fit(cluster_final_data)
[In]: model_k6.transform(cluster_final_data).groupBy('prediction').count().show()
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figl_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figl_HTML.jpg)

From our data, we see that there is one cluster in which the majority of users belong. Apart from that, there are five more clusters with lesser values. Now, we add this prediction to the existing dataframe, to be able to visualize the clusters.

```
[In]: model_k6.transform(cluster_final_data).show()
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figm_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figm_HTML.jpg)

```
[In]: cluser_prediction=model_k6.transform(cluster_final_data)
[In]: cluser_prediction.printSchema()
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Fign_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Fign_HTML.jpg)

Because we’re dealing with multiple dimensions, it will become difficult to visualize the data with cluster numbers. Therefore, we reduce the total number of dimensions, using the PCA (principal component analysis) technique . In previous chapters, you have already seen how to use PCA. We now reduce the original number of features from 21 to just 3, using PCA, as shown following.

```
[In]: from pyspark.ml.feature import PCA
[In]: from pyspark.ml.linalg import Vectors
[In]: pca = PCA(k=3, inputCol="scaledFeatures", outputCol="pca_features")
[In]: pca_model = pca.fit(cluser_prediction)
[In]: result = pca_model.transform(cluser_prediction).select('user_id_music category',"pca_features",'prediction')
[In]: result.show(truncate=False)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figo_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figo_HTML.jpg)

Finally, we convert the PCA dataframe to a Pandas dataframe, in addition to creating separate columns (x, y, z) from the PCA feature column, by making individual columns from the list.

```
[In]: clusters = result.toPandas().set_index('user_id_music category')
[In]: clusters.head(10)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figp_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figp_HTML.jpg)

```
[In]: clusters[['x','y','z']]=pd.DataFrame(clusters.pca_features.values.tolist(), index= clusters.index)
[In]: del clusters['pca_features']
[In]: clusters.head(10)
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Figq_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Figq_HTML.jpg)

Now that we have three-dimensional (3D) data representing the original features, and the cluster prediction by k-means, we can use 3D plotting techniques, to visualize these clusters on a 3D plot.

```
[In]: from mpl_toolkits.mplot3d import Axes3D
[In]: cluster_vis= plt.figure(figsize=(10,10)).gca(projection='3d')
[In]: cluster_vis.scatter(clusters.x, clusters.y, clusters.z, c=clusters.prediction)
[In]: cluster_vis.set_xlabel('x')
[In]: cluster_vis.set_ylabel('y')
[In]: cluster_vis.set_zlabel('z')
[In]: plt.show()
[Out]:
```

![../images/478390_1_En_7_Chapter/478390_1_En_7_Fig5_HTML.jpg](images/478390_1_En_7_Chapter/478390_1_En_7_Fig5_HTML.jpg)

Figure 7-5

Clusters Visualization

One of the follow-up activities after this clustering exercise can be to assign different personas to the groups, based on the attribute values. This can be a manual activity and can take some time to come up with meaningful personas. Once created, businesses can use these in numerous ways to market and target specific users.

## Conclusion

In this chapter, you learned the difference between supervised and unsupervised machine learning techniques. You also saw how to build clusters from raw data, using k-means clustering. The method to find the optimal value of k and how to visualize the final clusters formed by the k-means algorithm were then explained.

# 8. Deep Learning Using PySpark

Deep learning has been in the limelight for quite a few years and is making leaps and bounds in terms of solving various business challenges. From image language translation to self-driving cars, deep learning has become an important component in the larger scheme of things. There is no denying the fact that lots of companies today are betting heavily on deep learning, as a majority of their applications run using deep learning in the back end. For example, Google’s Gmail, YouTube, Search, Maps, and Assistance all use deep learning in some form or other. The reason is deep learning’s incredible ability to provide far better results, compared to some other machine learning algorithms.

This chapter is divided into three parts. The first focuses on understanding the fundamentals and underlying operating principles of deep learning. The second part covers the training process of the deep learning model. Finally, in the third and final part, you will see how to build a multilayer perceptron, using Spark.

## Deep Learning Fundamentals

Before even getting into deep learning, we must understand what neural networks are, as deep learning is a sort of extension of neural networks. Neural networks are not new; in fact, they go way back to the 1950s, when researchers began working on them. Unfortunately, they hit a major roadblock, owing to limited computation capabilities at the time. In the recent past, neural networks have gained in popularity, due to major advancements in processing power and access to big data. The availability of super-powerful processing devices, such as GPUs and TPUs, has made it possible to run huge neural networks with better performance. From the data aspect, the availability of labeled data over the last few years has also helped immensely. More than any of the preceding reasons, it’s the unique ability of deep learning models to offer significant performance over other machine learning algorithms that has made deep learning the preferred, mainstream approach. Figure [8-1](#478390_1_En_8_Chapter.xhtml#Fig1) shows the evolutionary time line of artificial intelligence, machine learning, and deep learning.

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig1_HTML.png](images/478390_1_En_8_Chapter/478390_1_En_8_Fig1_HTML.png)

Figure 8-1

Deep learning time line

In supervised learning settings, there is specific input and corresponding output. The objective of machine learning algorithms is to use this data and approximate the relationship between input and output variables. In some cases, this relationship can be quite evident and easy to capture, but in realistic scenarios, the relationship between input and output variables is very complex and nonlinear in nature. To give an example, a self-driving car, the input variables can be as follows:

*   Terrain

*   Distance from nearest object

*   Traffic lights

*   Signboards

The output must be either turn or drive fast or slowly or apply brakes, etc. As you might imagine, the relationship between input variables and output variables is fairly complex in nature. Therefore, the traditional machine learning algorithm finds it hard to map this kind of relationship. However, deep learning outperforms other machine learning algorithms in such situations, as it is able to learn nonlinear features as well.

### Human Brain Neuron vs. Artificial Neuron

As mentioned, deep learning is an extension of neural networks only and is also known as deep neural networks. Neural networks are a little different than other machine learning algorithms. Neural networks are loosely inspired by neurons in the human brain. Neural networks are made up of artificial neurons. Although I don’t claim to be an expert on neuroscience or functioning of the brain, let me try to give you a high-level overview of how the human brain functions. You might already be aware of the fact that the human brain is made up of billions of neurons, with an incredible number of connections between them. Each neuron is connected by multiple other neurons in some way and repeatedly exchanges information (signals). Each activity that we undertake physically or mentally fires up a certain set of neurons in our brain. Every neuron is made up of three basic components:

*   Dendrites

*   Cell Body

*   Terminals

As you can see in Figure [8-2](#478390_1_En_8_Chapter.xhtml#Fig2), in a human brain neuron, the dendrites are responsible for receiving signals from other neurons. They act as receivers of the particular neuron and pass information to a cell body, where the specific information is processed. Now, based on the level of information, it either activates (fires up) or doesn’t trigger. This activity depends on a particular threshold value of the neuron. If the incoming signal value is below that threshold, it will not fire; otherwise, it activates. Finally, the third component is terminals, which are connected to the dendrites of other neurons. Terminals are responsible for passing on the output of a particular neuron to other relevant connectors.

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig2_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig2_HTML.jpg)

Figure 8-2

Neuron of the human brain

Artificial neurons, on the other hand, consist mainly of two parts: one is summation, and the other is activation, as shown in Figure [8-3](#478390_1_En_8_Chapter.xhtml#Fig3). This is also known as a perceptron. Summation refers to adding all the input signals, and activation refers to whether the neuron will trigger, based on the threshold value.

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig3_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig3_HTML.jpg)

Figure 8-3

Parts of an artificial neuron

Let’s say we have two binary inputs (X1, X2) and weights of their respective connections (W1, W2), as shown in Figure [8-4](#478390_1_En_8_Chapter.xhtml#Fig4). The weights can be considered similar to coefficients of input variables in traditional machine learning. These weights indicate how important the particular input feature in the model is. The summation function calculates the total sum of the input.

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig4_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig4_HTML.jpg)

Figure 8-4

Inputs and weights in an artificial neuron

The activation function then uses this total summated value and gives a certain output. Activation is sort of a decision-making function. Based on the type of activation function used, it gives an output accordingly. There are different types of activation functions that can be used in a neural network layer.

### Activation Functions

Activation functions play a critical role in neural networks, as the output varies, based on the type of activation function used. There are typically three main activation functions that are widely used: sigmoid, hyperbolic tangent, and rectified linear unit.

#### Sigmoid

This activation function ensures that the output is always between 0 and 1, irrespective of the input, as shown in Figure [8-5](#478390_1_En_8_Chapter.xhtml#Fig5). That’s why it is also used in logistic regression, to predict the probability of an event.

![$$ f(x)=\frac{1}{1+{e}^{-x}} $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equa.png)

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig5_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig5_HTML.jpg)

Figure 8-5

Sigmoid activation function

#### Hyperbolic Tangent

Hyperbolic tangent activation (tanh) ensures that the output value remains between -1 to 1, regardless of the input, as shown in Figure [8-6](#478390_1_En_8_Chapter.xhtml#Fig6). Following is the tanh formula:

![$$ f(x)=\frac{e^{2x}-1}{e^{2x}+1\ } $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equb.png)

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig6_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig6_HTML.jpg)

Figure 8-6

Tanh activation function

#### Rectified Linear Unit

Rectified linear units (ReLUs) have been increasingly popular over the last few years and have become the default activation function. A ReLU is very powerful, as it produces values between 0 and ∞. If the input is 0 or less than 0, the output is always going to be 0, but for anything more than 0, the output is similar to the input, as shown in Figure [8-7](#478390_1_En_8_Chapter.xhtml#Fig7). The formula for a ReLU is

![$$ f(x)=\max \left(0,x\right) $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equc.png)

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig7_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig7_HTML.jpg)

Figure 8-7

Rectified linear unit

### Neuron Computation

Now that we have a basic understanding of different activation functions, let’s consider an example, to understand how the actual output is calculated inside a neuron. Let’s say we have two inputs, X1 and X2, with values of 0.2 and 0.7, respectively, and the weights are 0.05 and 0.03\. The summation function calculates the total sum of incoming input signals, as shown in Figures [8-8](#478390_1_En_8_Chapter.xhtml#Fig8) and [8-9](#478390_1_En_8_Chapter.xhtml#Fig9).

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig8_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig8_HTML.jpg)

Figure 8-8

Neuron computation

The summation is as follows:

![$$ sum=X1\ast W1+X2\ast W2 $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equd.png)

![$$ sum=0.2\ast 0.05+0.7\ast 0.03 $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Eque.png)

![$$ sum=0.01+0.021 $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equf.png)

![$$ sum=0.031 $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equg.png)

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig9_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig9_HTML.jpg)

Figure 8-9

Summation

The next step is to pass this sum through an activation function. Let’s consider using a sigmoid function, which returns values between 0 and 1, irrespective of the input. The sigmoid function will calculate the value, as follows:

![$$ f(x)=\frac{1}{\left(1+{e}^{-x}\right)} $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equh.png)

![$$ f(sum)=\frac{1}{\left(1+{e}^{- sum}\right)} $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equi.png)

![$$ f(0.031)=\frac{1}{\left(1+{e}^{-0.031}\right)} $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equj.png)

![$$ f(0.031)=0.5077 $$](images/478390_1_En_8_Chapter/478390_1_En_8_Chapter_TeX_Equk.png)

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig10_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig10_HTML.jpg)

Figure 8-10

Neuron Activation

So, the output of this single neuron is equal to 0.5077\. Now that we know how a single neuron operates, let’s quickly go over how multiple connected neurons work together to calculate the output.

## Training Process: Neural Network

When we combine multiple neurons, we end up with a neural network. Most simple and basic neural networks can be built using just input and output neurons, as shown in Figure [8-11](#478390_1_En_8_Chapter.xhtml#Fig11).

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig11_HTML.png](images/478390_1_En_8_Chapter/478390_1_En_8_Fig11_HTML.png)

Figure 8-11

Basic neural network

The challenge with using a neural network such as this is that it can only learn linear relationships and cannot perform well in cases in which the relationship between input and output is nonlinear. As we have seen, in real-world scenarios, the relationship is hardly simple and linear. Therefore, we must introduce an additional layer of neurons between the input and output layer, in order to increase the network’s capacity to learn different kinds of nonlinear relationships. This additional layer of neurons is known as a hidden layer, as shown in Figure [8-12](#478390_1_En_8_Chapter.xhtml#Fig12). It is responsible for introducing nonlinearities into the learning process of the network. Neural networks are also known as universal approximators, because they have the ability to approximate any relationship between input and output variables, no matter how complex and nonlinear in nature. A lot depends on the number of hidden layers in the networks and the total number of neurons in each hidden layer. Given sufficient numbers of hidden layers, a network can perform brilliantly at mapping this relationship.

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig12_HTML.png](images/478390_1_En_8_Chapter/478390_1_En_8_Fig12_HTML.png)

Figure 8-12

Multiple layer neural network

A neural network is all about various connections (red lines) and different weights associated with these connections. The training of neural networks primarily includes adjusting these weights in such a way that the model can make predictions with a higher degree of accuracy. To understand how neural networks are trained, let’s break down the steps of network training.

> Step 1\. Take the input values and calculate the output values that are passed to hidden neurons, as shown in Figure [8-13](#478390_1_En_8_Chapter.xhtml#Fig13). The weights used for the first iteration of sum calculation are generated randomly.

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig13_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig13_HTML.jpg)

Figure 8-13

Neural network training process

> An additional component that is passed is the bias neuron input, as shown in Figure [8-14](#478390_1_En_8_Chapter.xhtml#Fig14). This is mainly used when you want to have some non-zero output for even the zero input values.

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig14_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig14_HTML.jpg)

Figure 8-14

Bias component

> Step 2\. The hidden layer neurons now go through the same process to calculate the output, using the inputs from the previous layer (input layer). This hidden layer output acts as an input for the final output neuron (red) calculation, as shown in Figure [8-15](#478390_1_En_8_Chapter.xhtml#Fig15).

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig15_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig15_HTML.jpg)

Figure 8-15

Output calculation

> Step 3\. Once we have the final output, it is compared with the actual output, and the error is backpropagated to the network, to adjust the weights of the connections so as to reduce the overall error on the training data, as shown in Figure [8-16](#478390_1_En_8_Chapter.xhtml#Fig16). This process is known as backpropagation.

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig16_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig16_HTML.jpg)

Figure 8-16

Backpropagation

> Step 4\. Weights of the connections are readjusted according to the output, to minimize the overall errors made by the network, to the point that there is no further reduction of error on the training data.
> 
> Step 5\. Now that we have the final version of the weights, a new output value is calculated, based on updated weights, by the network, as shown in Figure [8-17](#478390_1_En_8_Chapter.xhtml#Fig17).

![../images/478390_1_En_8_Chapter/478390_1_En_8_Fig17_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Fig17_HTML.jpg)

Figure 8-17

Final output

## Building a Multilayer Perceptron Model

When it comes to using Deep Learning in Spark, there are multiple options.Depending on the exact requirement and available infrastructure, the relevant approach can be used. On a high level,there are close to 4 important deep learning libraries which can be used with Spark.

1.  Spark’s MLlib

2.  TensorflowOnSpark

3.  Deep Learning Pipelines

4.  DeepLearning4J

For simplicity, we will build a multilayer perceptron, using Spark. The dataset that we are going to use for this exercise contains close to 75k records, with some sample customer journey data on a retail web site. There are 16 input features to predict whether the visitor is likely to convert. We have a balanced target class in this dataset. We will use `MultilayerPerceptronClassifier` from Spark’s Machine Learning library. We start by importing a few important dependencies.

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.appName('deep_learning').getOrCreate()
[In]: import os
[In]: import numpy as np
[In]: import pandas as pd
[In]: from pyspark.sql.types import *
```

Now we load the dataset into Spark, for feature engineering and model training. As mentioned, there are 16 input features and 1 output column (`'Orders_Normalized'`).

```
[In]: data = spark.read.csv('dl_data.csv', header=True, inferSchema=True)
[In]: data.printSchema()
[Out]:
```

![../images/478390_1_En_8_Chapter/478390_1_En_8_Figa_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Figa_HTML.jpg)

We change the name of the label column from `'Orders_Normalized'` to `'label'`, to be able to train the model.

```
[In]: data = data.withColumnRenamed('Orders_Normalized', 'label')
[In]: data.printSchema()
[Out]:
```

![../images/478390_1_En_8_Chapter/478390_1_En_8_Figb_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Figb_HTML.jpg)

Because we are dealing with both numerical and categorical columns, we must write a pipeline to create features combining both for model training. Therefore, we import `Pipeline`, `VectorAssembler`, and `OneHotEncoder`, to create feature vectors. We will also import `MultiClassificationEvaluator` and `MultilayerPerceptron`, to check the performance of the model.

```
[In]: from pyspark.ml.feature import OneHotEncoder, VectorAssembler, StringIndexer
[In]: from pyspark.ml import Pipeline
[In]: from pyspark.sql.functions import udf, StringType
[In]: from pyspark.ml.evaluation import MulticlassClassificationEvaluator
[In]: from pyspark.ml.classification import MultilayerPerceptronClassifier
```

We now split the data into train, test, and validation sets, for training of the model.

```
[In]: train, validation, test  = data.randomSplit([0.7, 0.2, 0.1], 1234)
```

We create separate lists of categorical columns and numeric columns, based on datatypes.

```
[In]: categorical_columns = [item[0] for item in data.dtypes if item[1].startswith('string')]
[In]: numeric_columns = [item[0] for item in data.dtypes if item[1].startswith('double')]
[In]: indexers = [StringIndexer(inputCol=column, outputCol='{0}_index'.format(column)) for column in categorical_columns]
```

We now create consolidated feature vectors, using `VectorAssembler`.

```
[In]: featuresCreator = VectorAssembler(inputCols=[indexer.getOutputCol() for indexer in indexers] + numeric_columns, outputCol="features")
[In]: layers = [len(featuresCreator.getInputCols()), 4, 2, 2]
```

The next step is to build the `MultilayerPerceptron` model . One can play around with different hyperparameters, such as number of layers and maxiters, to improve the performance of the model.

```
[In]: classifier = MultilayerPerceptronClassifier(labelCol='label', featuresCol="features", maxIter=100, layers=layers, blockSize=128, seed=1234)
```

Now that we have defined every stage, we add all these steps to the pipeline and run it on the training data.

```
[In]: pipeline = Pipeline(stages=indexers + [featuresCreator, classifier])
[In]: model = pipeline.fit(train)
```

We now calculate the predictions of the model on train, test, and validation datasets.

```
[In]: train_output_df = model.transform(train)
[In]: validation_output_df = model.transform(validation)
[In]: test_output_df = model.transform(test)
[In]: train_predictionAndLabels = train_output_df.select("prediction", "label")
[In]: validation_predictionAndLabels = validation_output_df.select("prediction", "label")
[In]: test_predictionAndLabels = test_output_df.select("prediction", "label")
```

We define three different metrics, to evaluate the performance of the model.

```
metrics = ['weightedPrecision', 'weightedRecall', 'accuracy']
[In]: for metric in metrics:
evaluator = MulticlassClassificationEvaluator(metricName=metric)
print('Train ' + metric + ' = ' + str(evaluator.evaluate(train_predictionAndLabels)))
print('Validation ' + metric + ' = ' + str(evaluator.evaluate(validation_predictionAndLabels)))
print('Test ' + metric + ' = ' + str(evaluator.evaluate(test_predictionAndLabels)))
```

As you can see, the deep learning model is doing reasonably well on the test data, based on the input signal.

```
[Out]:
```

![../images/478390_1_En_8_Chapter/478390_1_En_8_Figc_HTML.jpg](images/478390_1_En_8_Chapter/478390_1_En_8_Figc_HTML.jpg)

## Conclusion

This chapter covered the internals of the basic building blocks of neural networks—artificial neurons—and the entire training process of a neural network. Different ways in which deep learning models can be constructed were mentioned, and, using Spark, a multilayer perceptron model was built.

### Index

### A

Activation function Aggregation collect functionality groupBy function operations orderBy functionality PySpark sort functionality sql function stages Airflow framework graphs directed undirected installation setup (Mac) using Docker workflows Akaike information criterion (AIC)

### B

Baseline method Bash operator Batch data Batch *vs.* stream batch data spark streaming stream processing Big data processing Binarizer BinaryClassEvaluator Binning/bucketing

### C

Chi-square test Classification model, build dataframe, explore data transformation hyperparameter tuning load dataset MLlib model training training and test sets Cloud-based environments Cloud operator Collect list Command-line interface (CLI) Contingency table Core architecture, Spark components graph X MLlib programming language APIs resource management cluster mangers task worker SQL storage structured streaming Correlations Cyclic graph

### D, E

DAG SeeDirected acyclic graph (DAG Databricks cluster creation list login notebook creation spark adoption survey web page Dataframe clustering crosstab function StandardScaler VectorAssembler Dataframe, subset filter select Data processing aggregations dataframe, subset joins pivoting sparkSession object UDFs window functions Decision tree classifier build and train test data, evaluation Decision tree regression build and train model performance, evaluate overfitting Deep learning activation function hyperbolic tangent ReLUs sigmoid multilayer perception model neural network *vs.* artificial networks backpropagation bias component challenge multiple layer output training process neuron computation time line Directed acyclic graph (DAG) creation default arguments dependencies, mentioning airflow code details tab graph view initialization tree view trigger UI import libraries information flow operators python extension tasks declaring Dockers

### F

File folders/directory fillna function

### G

Generalized linear model (GLM) build and train link function model performance, evaluate Gradient boosted tree (GBT) classifier build and train test data, evaluation Gradient-boosted tree (GBT) regressor boosting build and train model performance, evaluate Graph data structure

### H

Hadoop MapReduce framework Hyperbolic tangent activation Hyperparameter tuning

### I

Internet of things (IoT)

### J

Joins, dataframe

### K

K-means clustering 3D data existing dataframe multiple elbows Pandas dataframe PCA

### L

Linear regression model data information build and train dataset evaluate, test data feature engineering read dataset sparkSession object split dataset types Logistic regression build and train feature engineering read dataset test data, evaluation train and test datasets training data, evaluation

### M

Machine Learning library (MLlib) chi-square test classification models correlations data analysis distributed training MaxAbsScaler Messaging systems Min-max scaling MLlib SeeMachine Learning library (MLlib MultilayerPerceptron model Multivariable linear regression

### N, O

Naive Bayes (NB) classifiers build and train conditional probability test data, evaluation Normalization

### P, Q

Pandas UDFs pivot function Principal component analysis (PCA) Python operator

### R

Random forest classifier build and train test data, evaluation Random forest regressors build and train model performance, evaluate Rectified linear units (ReLUs) Resilient Distributed Datasets (RDDs) Root mean squared error (RMSE)

### S

Scaling Sigmoid activation function Spark application architecture See alsoCore architecture, Spark aspects of data cluster data collection data processing data storage history Spark environment databricks Dockers local setup SparkSession object dataframes, creation null values Spark streaming batch processing component data flow framework Static *vs.* streaming DataFrame Stream processing Structured streaming application batch processing dataframe data input data processing definition final output joins local directory operations process Spark SQL engine Supervised machine learning binary class multi class regression task type Support vector machines (SVMs) build and train test data, evaluation

### T

Transformations Binarizer binning/bucketing MaxAbsScaler min-max scaling normalization PCA standard scaling

### U, V

Unsupervised machine learning association attempts *vs* . goals, sample data clustering groups, data K-means SparkSession, import User-defined functions (UDFs)

### W, X, Y, Z

Windowed aggregates