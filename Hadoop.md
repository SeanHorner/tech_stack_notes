# Hadoop Notes

## Basic Differences between HDFS and RDBMS

- HDFS is districuted across a cluster of machines, giving it faster network throughput than a single RDBMS machine.
- Hadoop uses a file system (HDFS) and can therefore hold any type of file, not just table data.
- Hadoop offers some limited data processing abilities for information stored on the cluster, RDBMS does not.
- Hadoop uses the "schema on read" paradigm, where data's format is determined as it's being read (allowing it to be anything that the developer needs it to be), where as RDBMS enforces "schema on write", that is the data must be in the appropriate format to be written to the database.
- Hadoop has very fast write speeds, because no schema verification has to happen during the write, but RDBMS has very fast read speeds because the schema is already known (since it's enforced by the database).
- Hadoop is open source software (therefore free); RDBMSs are proprietary software, therefore require a license.
- Hadoop is best used for data discovery, data analytics, or OLAP systems; RDBMSs are best for OLTP (OnLine Transactional Processing) systems.

## 5 V's of Big Data

- Volume:     Big data handles data growing at an exponential rate (on the Petabyte, Exabyte scale).
- Velocity:   Big data handles large amount of data coming in fast (100's MBPS - 10's GBPS).
- Variety:    Big data handles data that is various in size, type, and format.
- Veracity:   Big data handles data that may not be perfectly accurate or may be incomplete (Still coming in).
- Value:      Big data needs to turn all of this data into something valuable/useful to an organization.

## What is Hadoop? What are it's components?

Hadoop is Apache's open source software to deal with the "Big Data Problem". It provides various tools useful for storing, handling, and processing large datasets. Its main components are a Storage Unit (HDFS) and a Processing Framework (YARN).

## What are HDFS and YARN? What are their components?

- HDFS is the Hadoop Distributed File System and it is the Storage Unit of Hadoop, meaning it is the software that handles storing data files on the Hadoop cluster. It is composed of the NameNode and DataNode.
  - NameNode is the master node in the distributed file system. It keeps track of the data on the cluster including such metadata as which blocks specific data is hosted at, the replication of data, etc. There are also Secondary NameNodes which are used as backups in the case of a primary NameNode failing. 
  - DataNode are the worker nodes in the cluster (i.e. the DataNode software run on each worker machine) and it is responsible for storing on and retrieving from local storage on the machine as requested.
- YARN is the Yet Another Resource Manager and it is the processing framework for Hadoop, i.e. it manages resources on the cluster and provides an execution platform for jobs submitted to the cluster. It is composed of the ResourceManager and NodeManager.
  - ResourceManager is the master node of the YARN and it receives processing requests for the cluster and then hands out parts of the processing request to the NodeManagers on each worker, where the actual processing takes place. It allocates resources to applications based on the needs.
  - NodeManager is the worker node software for YARN (i.e. it is the software that runs on each worker node to interact with the ResourceManager). It is responsible for the execution of the processing tasks passed to the worker node by the ResourceManager.

## What are the daemons and their roles in the Hadoop cluster?

- NameNode
  - Main node of the Hadoop cluster, responsible for maintaining metadata on HDFS data location.
- DataNode
  - Software on each worker node, responsible for storing and retrieving data from local storage.
- Secondary NameNode
  - A backup NameNode that takes over in the case of a failure on the primary.
- ResourceManager
  - Central processing software for the YARN, receives processing jobs and parses them to workers.
- NodeManager
  - Software on each worker machine that receives and executes processing jobs then reports back to the ResourceManager.
- JobHistoryServer
  - Maintains information on the MapReduce jobs that have been run on the server.

## What are the differences between HDFS and NAS storage?

HDFS stores files in pieces (shards) across the many member machines of a cluster, similarly to how NASs use RAIDed drives to spread out data across many drives, both of which increase file retrieval speed. Although both provide speed gains, the ability to leverage multiple network interface cards on the multiple worker nodes makes network access to HDFS data much faster than the NAS, which has to route all traffic through a single network interface.

HDFS is extremely redundant in ways that NASs cannot be. While RAID 6 offers double fault protection, the same level of protection offered by HDFS, after a fault occurs the NAS must be spun down and the faulty drive replaced to return to the original safety level. By contrast, the HDFS cluster offers double fault tolerence, by maintaining data in 3 replicas, but extends that even further by spreading the replications out amongst the cluster so that the liklihood of two machines faulting out will take two replications of the data with them extremely unlikely. Also HDFS clusters don't need to spin down to handle faults, the NameNode will simply note what data was on the faulted machine and create new replicas to replace that data on other machines. Also, RAIDs cannot be expanded with new drives, drives can only be replaced and reslivered one by one, but with an HDFS cluster new machines (and therefore more storage) can be added ("commissioned") whenever needed.

Hadoop can perform MapReduce operations on the cluster where the data exists in HDFS, but for NASs, the data needs to be moved to a compute machine before being processed.

HDFS uses commodity (i.e. cheap) hardware for its cluster, NASs require specialty hardware which is often quite expensive.

## Differences between Hadoop 1 and Hadoop 2

In Hadoop 1 there is only one active NameNode, creating a single point of failure, but in Hadoop 2 there are both active and passive NameNodes, meaning if the active faults out then a passive NameNode can step in and take over, making version 2 more highly available.

In Hadoop 1 there is only the MapReduce version 1 (MRv1) and can only be used to run MapReduce jobs. Hadoop 2 has the YARN resource manager which makes it possible to run many different applications (including Hive and Spark) on top of the Hadoop cluster.

## What happens if two users try to access the same file in HDFS?

HDFS supports exclusive writes, meaning that once one user opens a file to write to it, the NameNode gives that user a lease to modify that file. If another user attempts to open that file for writing, the NameNode will see the lease given to the first user and not allow any writes by the second user until the lease is revoked or times out.

## What does the NameNode do in the case of a DataNode failure?

The NameNode receives periodic signals from the DataNodes, called "heartbeat signals", that indicate that the node is alive and their current workload status/availability. If a node fails to send heartbeat signals for a specific amount of time then the NameNode considers the worker machine dead/faulted out, it drops it from the cluster, finds which blocks were on that node through a block report, and makes replications of all of the data that was on that node on healthy workers.

## What is a checkpoint?

A checkpoint is the most recent FSImage (File System Image) of an HDFS cluster that a NameNode uses when a cluster is restarted or when recovering from a failure. The FSImage is essentially the metadata of the HDFS cluster. Checkpointing is the process of combining the currently saved checkpoint with the edit log to create a new, up-to-date checkpoint. This process is done by the Secondary NameNode.

## How does HDFS perform fault tolernce?

The NameNode ensures that there are at least 3 replicas of data stored on the DataNodes of the cluster. 3 is the default number of replications, this can be changed if desired with the key dfs.replication in the hdfs-site.xml file in config file. If any DataNode faults out, the NameNode ensures that new replicas of any data that were on that NameNode are re-replicated on live nodes.

## Can NameNode and DataNode be commodity hardware?

The DataNodes of a cluster can definitely be commodity hardware, that's the benefit of using cluster computing, but the NameNode should have higher technical capabilities because: 
  1. it's much more of an issue to have a NameNode fault out than a DataNode and 
  2. the NameNode requires a lot of RAM to hold the FSImage in memory (roughly 150 bytes per file, directory, or block).

## Why is HDFS more useful for large datasets and not multiple small files?

The NameNode has to hold the entire FSImage worth of metadata in its RAM, so there is an inherent limit on the number of files that can be held in an HDFS cluster, since each file and directory take up about 150 bytes of RAM on the NameNode machine. Because of this maintaining many small files is very RAM-intensive on the NameNode, where as having one large dataset file only takes up a few bytes of RAM.

## What is a block? And how large are blocks in HDFS 1.0? 2.0?

A block is the minimum size piece of information that is held in the HDFS cluster. Every file that is stored on the cluster is broken up into blocks and spread out/replicated over the cluster. For example, if a 260 MB file is placed on the cluster it is broken into pieces based on the size of a block (dependent on which HDFS version: v1 = 64 MB per block; v2 = 128 MB per block). So if the file is being stored on a v2 HDFS cluster, then it would be broken up into 3 128MB blocks, the first two completely filled, the last on with just the remaining 20 MB of data.

## What does the 'jps' command do?

'jps' stands for the "Java virtual machine Process Status tool" and it is the commandline tool that lists all running JVM processes. As such it should list all of the Hadoop daemons that we have running at any given moment, as well as any other JVM processes running.

## How do you define "rack awareness" in Hadoop?

"Rack awareness" is the concept that Hadoop can be told which nodes are in a server rack together and how close racks neighbor each other. This information is very useful when trying to ensure that all three replications of a dataset are safe from faults. For example, if HDFS ensured that all three replications of data were on different nodes, but those three nodes were all in the same rack together, a power failure on the rack would cause all three replications to be lost. As such Hadoop uses rack awareness to ensure that replications are spread out in a fault tolerant manner, generally, 2 replicas of the data inside of a rack and 1 on a nearby rack. This means that two replicas are nearby each other and only need to use the network interior to the rack (i.e. not the network connecting racks together) to write any changes to the replicas while still maintaining a level of fault tolerence by keeping a replica in a different rack. 

## What is "speculative execution"?

Speculative execution is the process in which the master node, having noticed that one of the worker nodes is taking a long time processing an assigned task, will assign that task to another node that is already done with its tasks. The master will accept the completed results of whichever node finishes first and disregard the other. In this way the cluster won't hang on waiting for one node's results in the event that it is running slowly/in a diminished capacity.

## How does one restart the NameNode daemon or all daemons in a cluster?

Inside the install directory there is a directory called /sbin in which there are scripts pertaining to the operation of the cluster. Of these scripts, there are three which can be used to reset the NameNode daemon. 
  1. The refresh-namenodes.sh which will restart and reinitialize the NameNodes (primary and secondary) in the cluster. 
  2. The hadoop-daemon.sh script which, when passed with the options "stop namenode" and then "start namenode" will stop and then restart the namenode on the cluster.
  3. The stop-all.sh and start-all.sh scripts which will stop and then start all daemons, respectively.
  
## What is the difference between an HDFS block and an input split?

An HDFS block is an actual, physical division of data, i.e. a 128 MB span of storage space completely distinct from other blocks. An input split is a logical division of data, i.e. it is the first 128 MB of an input file, which is then assigned to a mapper node.

## What are the three modes in which Hadoop can be run?

  1.  **Standalone (local) mode** - This is a single machine mode and the default mode if no configurations are made. In this mode, all of the daemon processes are run as a single JVM process and Hadoop uses local storage instead of HDFS.

  2.  **Pseudo-distributed mode** - This is also a single machine mode, except that all of the service daemons, master and worker, are launched and running on the single machine.

  3.  **Fully-distributed mode** - This is the normal, production, multi-machine deployment of a Hadoop cluster with a main master node and some number of worker nodes, each running their respective service daemons.

## What is MapReduce? What is the syntax for running a MapReduce job?

MapReduce is a data processing framework that processes large datasets by first mapping some function onto the initial data in the "Mapper" stage and then reducing the results of that function into a more concise and coherent output in the "reducer" stage.

To run a map reduce job on a Hadoop cluster first a programmer must write a java JAR file containing the Map and Reduce stage logic. Then it is passed to the cluster with the following syntax: 
~~~
job_logic_jar_file.jar /path_to_input_data /path_to_output_directory .
~~~

## What are the main configuration parameters in a "MapReduce" program?

- The location of the input data on either the local or distributed file system.
- The location where output files should be placed (either local or on the dfs).
- The format that the input data takes.
- The format that the output data should take.
- A class containing the Map function's logic.
- A class containing the Reduce function's logic.
- A JAR file containing the mapper, reducer, and driver (main program) classes as one.

## Why can "aggregation/combination" be performed in the Mapper stage and must be done in the Reducer stage?

This restriction occurs because the input data is split amongst the Mapper nodes arbitrarily (by input split) and so even if an aggregation is done on each mapper (called a Combiner stage) there is no guarantee that all of the occurrences of one key (let's say a specific word) will be accounted for on one Mapper node. To ensure that all occurrences are accounted for during aggregation the output from the Mapper nodes are shuffled and sent to Reducer nodes by hashing their key values, thus ensuring that every occurrence of a key is sent to the same Reducer node to be aggregated together.

## What is the purpose of the "RecordReader" in Hadoop?

An input split defines a small amount of the imput that will be sent to a specific Mapper node, but it doesn't explain how to read or initially process that input data. For that purpose we need a RecordReader which recieves the input split declarations, retrieves the appropriate segment of the input file, pre-processes it into a format that the Mapper logic is expecting, and hands it off to the Mapper node. This process is defined in the Input Format stage of the program logic.

## Explain distributed cache in the MapReduce framework.

The distributed cache is a method that the MapReduce framework offers to cache and distribute files to each of the worker nodes in the cluster during the MapReduce job so that the file can be accessed in local memory on each. For example, a word list that the mappers need to check against.

## How do reducers communicate with one another?

This is a trick questions, reducers don't communicate with one another, they run in isolation.

## What does a MapReduce Partitioner do?

The partitioner is responsible for ensuring that identical keys all end up on the same reducer during the shuffle stage before the Reduce stage thus ensuring that all like keys are combined for the final output. Custom partitioners can be written to determine how Mapper stage output should be sent to Reducers.

## How do you write a custom partitioner?

To write a custom partitioner for a MapReduce job, you must:
  1.  Create a class that extends the Partitioner Class.
  2.  Override the getPartition method in the MapReduce wrapper.
  3.  Add the custom partitioner class into the job by using the setPartitioner method or add it as a config file.

##  What is a Combiner?

A Combiner stage is a reduce step that happens on each Mapper node after the Map tasks have finished. Effectively it pre-reduces the Mapper stage output to reduce traffic during the pre-Reduce stage shuffle and to reduce the amount of processing that has to be done in the Reduce stage.

## What is the "SequenceFileInputFormat"?

The SequenceFileInputFormat is an input file format for dealing with sequence files. It is a specialized, compressed binary file format for use while doing sequential MapReduce jobs, that is taking an input, feeding it through a MapReduce job, then using the output from that job as input for another MapReduce job, and so on. 
