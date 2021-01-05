# Spark Notes

## Compare Hadoop and Spark

-	In terms of speed, Spark is 10s if not 100s of times faster than Hadoop's MapReduce, mainly because Spark does processing in memory (RAM) and has much faster access to the data than accessing it from a Hadoop cluster.
- In terms of processing, Hadoop can only process data in batches as MapReduce jobs, but Spark has the ability to be used both in batch processing as well as real-time streaming, which makes it immensely more useful for certain use cases.
-	In terms of difficulty to learn, Spark has APIs for many languages as well as abstractions like Spark SQL which make it easy for data scientists completely devoide of programming knowledge to leverage it's data processing power. By contrast, Hadoop, while supporting many languages, requires a very specific format to program its MapReduce engine.
-	In terms of data recovery abilities, Spark allows data recovery by recovering/recalculating RDDs from their lineages; Hadoop is a fault-tolerant distribution across a cluster.
-	In terms of interactivity, Spark is extremely interactive, it has many different modes and use cases that it can run in. By comparison, Hadoop has no built-in interactivity fuctionality, it requires Hive or Pig to run analyses on the data it holds.

## What is Apache Spark?

-	Spark is an open-source cluster computing framework for real-time processing with a large and active community. Spark provides an interface for programming entire clusters with implicit data parallelism and fault-tolerance with a focus on Big Data processing.

## What are the key features of Apache Spark?

1. Polyglot: It is possible to write Spark code in many languages including Java, Scala, Python, and R, making it accessible to many programmers in a language they know well.

2. Speed: Spark runs up to 100 times faster than Hadoop MapReduce jobs do in large-scale processing. Spark does this by using in-memory processing and high control over partitioning, which helps reduce network traffic throttling processing speed.

3. Multiple Format Support: Spark has built-in support for many data types including: Parquet, HDFS files, CSV, TSV, JSON, Hive tables, and Cassandra.

4. Lazy Evaluation: Spark saves time and processing power by performing evaluations lazily, that is RDDs are only processed once they need to be and before that Spark simply keeps tabs on what transformations are being done on an RDD.

5. Real-Time Computation: Spark's in-memory processing paradigm allows for literally real-time (i.e. extremely low latency) processing of data.

6. Hadoop Integration: Spark, being part of the Apache Big Data ecosystem, comes with built-in compatibility with Hadoop, both the distributed file system and the YARN resource manager, to run easily on Hadoop clusters.

7. Machine Learning: Spark's MLlib is a machine learning library that is extremely useful when creating machine learning algorithms, avoiding the need to have two separate tools for data processing and building a machine learning algorithm.

## What languages are supported by Apache Spark? What is the most popular?

-	Spark provides APIs for four major languages: Scala, Java, Python, and R.
- The most popular language is Scala, as that is the language Spark is written in.

## What are the benefits of Spark over MapReduce?

- MapReduce can only be run in an acyclic/non-iterative manner, where as Spark can iterate through a dataset.
- Spark's in-memory processing allows it to perform analyses 10 to 100 times faster than MapReduce, which uses persistent storage to hold data.
- MapReduce in Hadoop is highly dependent on disk, where as Spark is heavily dependent on RAM.
- MapReduce can only run analyses in batch processes, but Spark can run in batch processes, or streaming, or as SQL queries, or as machine learning algorithms, all from the same core.

## What is YARN?

Exactly as in Hadoop, YARN is Spark's resource manager which helps Spark to containerize and pass out pieces of a processing job to the different nodes of a cluster.

## Do you need to install Spark on all the nodes of a YARN cluster?

No, Spark runs ontop of YARN and works with YARN to split the job into pieces that each node can compute and pass back. There are some options that Spark can leverage when interacting with YARN to gain more granular control of the processing job, including:
- master
- deploy-mode
- driver-memory
- executor-memory
- executor-cores
- queue

## Is it beneficial to learn MapReduce if Spark is faster and more efficient?

Yes, since Spark and other big data processing tools (like Pig and Hive) convert their tasks into MapReduce jobs for processing. Understanding the underlying structure of how MapReduce algorithms work (and the possible shortcuts therein) can be very beneficial to any big data engineer. A great metaphore is how in math students are taught Riemann sums before learning definite integrals because it makes the underlying logic of integrals more accessible (and the pitfalls or unique situations that may happen therein) than just immediately jumping to integrals, since they are faster. Or even more basically, learning mental mathematic skills like addition, subtraction, multiplcation, and division instead of just telling students to use a calculator (as is often the default in much higher/harder math courses).

## Explain the concept of RDDS.

RDDs (Resilient Distributed Datasets) are fault-tolerant collections of operational elements that are run in parallel. The data contained in an RDD is immutable and distributed amongst the memory of the nodes in a cluster. RDDs are lazily evaluated in Spark (i.e. they are not calculated until they need to be) which contributes to Spark's speedy processing. There are two primary types of RDDs:
1.  **Parallelized Collections**: A collection of elements that is spread out among a cluster's nodes' memory and can be processed in parallel.
2.  **Hadoop Datasets**: These RDDs are comprised of the files in an HDFS directory and transformations done on them are performed on each of the records.

## How are RDDs created in Spark?

There are three main methods for creating RDDs:
1.  You can parallelize a collection in the Driver program.
2.  You can use the SparkContext's parallelize method:
~~~
val sc = SparkContext()
val DataArray = Array(2,4,6,8,10)
val DataRDD = sc.parallelize(DataArray)
~~~
3. By loading an external dataset from external storage like HDFS, HBase, or a shared file system.

Another method is to transform one from another RDD:
~~~
val NewRDD = DataRDD.filter(_ % 4 != 0)
~~~

## What is executor memory in a Spark application?

Executor memory is 
