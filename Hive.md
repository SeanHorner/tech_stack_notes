# Hive Notes

## What are some differences between Hive and HBase?

1. **Structure**
  - HBase is a table-like database built on top of HDFS.
  - Hive is a table-like data-warehousing infrastructure that can be used with HDFS.

2. **Operability**
  - HBase runs table operations (select, where, etc.) on its database.
  - Hive converts its queries into MapReduce jobs which it executes internally.

3. **Latency**
  - HBase provides very low latency for retrieving single rows from a large dataset.
  - Hive provides much better performance when running queries on the entirety of large datasets.

4. **Data Access**
  - HBase provides random access to data.
  - Hive provides random access to data.

## What languages can be used to write applications for Hive?

Hive supports Java, PHP, Python, C++, and Ruby by utilizing its Thrift server APIs.

## Where does Hive table data get stored?

Hive stores all internally managed table data in the warehouse directory, by default located at /user/hive/warehouse in HDFS although that can be changed by setting the hive.metastore.warehouse.dir parameter in the hive-site.xml configuration file.

## What is a metastore in Hive?

The metastore holds all of the metadata information from the RDBMS and ORM (Object Relational Model) layer that Hive uses for its tables.

## Why does Hive not store metadata information in HDFS?

Hive uses its RDBMS to hold metadata information in the metastore because it provides much lower latency, since read and write speeds in HDFS are much longer.

## What is the difference between local and remote metastores?

- A local metastore is one that runs in the same JVM as Hive and connects to a database running in a separate JVM, either on the local machine or a remote one.
- A remote metastore is one that runs in a different JVM from Hive (either on the local machine or a remote machine) and interacts with Hive through the Thrift server API. This method allows the user to have more than one metastore connected to Hive and thus more availability.

## What is the default database that Hive uses for the metastore?

Hive comes prepackaged with the Derby database as its default metastore option using the local disk for storage. This is called the embedded metastore configuration.

## What should you do if you need to let multiple clients connect to the same Hive metastore at the same time?

By default, Hive only allows a single connection at a time to the metastore. To allow multiple connections a standalone metastore must be used and the following settings must be changed:
- javax.jdo.option.ConnectionURL
- javax.jdo.option.ConnectionDriverName
- javax.jdo.ConnectionUserName should be set to the desired username
- javax.jdo.ConnectionPassword should be set to the desired password
- the approriate JDBC driver JAR for the desired database should be copied to Hive's lib directory or added to Hive's classpath

## What is the difference between an external table and a managed table?

An external table is one that is maintained outside of Hive while a managed table is one which Hive controls. The major difference between the two, other than their managing entity, is that when a managed table is dropped from Hive, all of its data is erased, but if the table is external to Hive, the data remains intact.

## Is it possible to change the location where managed tables are housed?

Yes, a managed table's data location can be changed by passing the -LOCATION <hdfs-path> clause when creating the table.

## When should a SORT BY be used instead of an ORDER BY?

The SORT BY clause should be used whenever the dataset is sufficiently large, since SORT BY uses multiple reducers to perform the sorting where as ORDER BY uses a single reducer.

## What is a partition in Hive?

A partition is a subsegmentation of data taken by specifying a column or set of columns on which to separate the table's rows by. In physical storage, partitions appear as subdirectories in the table's directory.

## Why is partitioning useful?

In Hive partitioning is useful for dividing our data into pertinent chunks for our analyses much more quickly than if we had to run the analysis on the whole dataset and then split out the pertinent rows. For example, if my dataset contained banking information over a long time period and I wanted to look for suspicious transactions in the month of February (e.g. double purchasing flowers) then partitioning the dataset on month would be a good way to avoid running my analysis on a large unneccessary part of the dataset.

## What is dynamic partitioning and when is it useful?

Dynamic partitioning is when partitioning is done at runtime, i.e. while table data is being loaded into Hive.

Dynamic partitioning is useful for:
- When a programmer cannot be sure (or doesn't want the tedious task of) enumerating every possible value for a column. For example, if I'm inserting COVID data into my Hive database and I want it partitioned on country, I may not want to list out all the possible countries in the dataset, e.g.  
~~~
hive>LOAD DATA INPATH '/hdfs_path_to_file' INTO TABLE t1 PARTITION(country="US")
hive>LOAD DATA INPATH '/hdfs_path_to_file' INTO TABLE t1 PARTITION(country="UK")
hive>LOAD DATA INPATH '/hdfs_path_to_file' INTO TABLE t1 PARTITION(country="Germany")
...
~~~

- The other is when loading data from an unpartitioned Hive table into a new partitioned one, to decrease query latency. For example, say I have a transaction_details table that contains customer ID, transaction amount, month of transaction, and country in this schema:  
~~~
hive>CREATE TABLE transaction_details (cust_id INT, amount FLOAT, month STRING, country STRING)
hive>ROW FORMAT DELIMITED
hive>FIELDS TERMINATED BY ',';
~~~  
With 50,000 rows inserted, my query for how much revenue is generated each month is taking way too long. To alleviate this I can move the current table to a newly partitioned table.
Create a new partitioned table:  
~~~
hive>CREATE TABLE partitioned_transactions (cust_id INT, amount FLOAT, country STRING)
hive>PARTITIONED BY (month STRING)
hive>ROW FORMAT DELIMITED
hive>FIELDS TERMINATED BY ',';
~~~  
Set the appropriate Hive settings to enable dynamic repartitioning:  
~~~  
hive>SET hive.exec.dynamic.partition = true;
hive>SET hive.exec.dynamic.partition.mode = nonstrict;
~~~  
And transfer data into the partioned table:  
~~~
hive>INSERT OVERWRITE TABLE partitioned_transactions PARTITION (month) SELECT cust_id, amount, country, month FROM transaction_details;
~~~  
**Note:** To insert into the partitioned table, you have to ensure that the partitioned column (in this case month) *must* be the last column listed in the SELECT statement.

## How can a partition be added to an already partitioned table?

Using the following ALTER command:
~~~
ALTER TABLE <part_table_name> ADD PARTITION (<part_col>=<new_part_value>) LOCATION '<part_table_directory>'
~~~
For the example in the previous section it would be:
~~~
ALTER TABLE partitioned_transactions ADD PARTITION (month='Dec') LOCATION '/partitioned_transactions'
~~~

## What is the default maximum dynamic partition that can be created by a mapper/reducer? Can it be changed?

By default, the maximum number of dynamic partitions that a mapper or reducer node can create is 100. This can be changed by the following command:
~~~
hive>SET hive.exec.max.dynamic.partitions.pernode = <value>
~~~
Or the total number of dynamic partitions that can be created with:
~~~
hive>SET hive.exec.max.dynamic.partitions = <value>
~~~

## How do you fix a 'FAILED ERROR IN SEMANTIC ANALYSIS: Dynamic partition strict mode...' error?

The appropriate settings haven't been changed to allow dynamic partitioning. To solve this, enter the following commands:
~~~
hive>SET hive.exec.dynamic.partition = true;
hive>SET hive.exec.dynamic.partition.mode = nonstrict;
~~~

## Why are buckets useful?

Buckets have two main useful situations:
1.  During a map side join, rows that share values of the key in the join condition need to be in the same partition, but if the join key is a different column than the partition key they may end up in different partitions from one another. In this case bucketing on the join key column is useful to ensure that rows with the same join key value are in the same bucket.
2.  Bucketing makes sampling of data a lot more efficient and therefore decreases query latency. **Note:** a column with low cardinality needs to be chosen to avoid bucket skew.

## How does Hive distribute rows into buckets?

Hive performs a hashing function on the value of the bucketing column and splits the rows into buckets based on a modulo of the number of buckets.
~~~
hash_function(bucketing_column) % <num_of_buckets> = bucket_number for row
~~~
The hashing function used is unique to the datatype of the column that is being hased, e.g. the hashing function used on integer datatypes returns the integer.

## What do buckets look like in HDFS?

Buckets appear as separate datafiles in the table directory on HDFS (as opposed to partitions which get their own sub-directories).

## What happens in Hive 0.x or 1.x if you try to use bucketing before setting hive.enforce.bucketing to true?

If the appropriate setting (listed) hasn't been set to true, then Hive won't guarantee that the number of reducers used for the CLUSTER BY command will be the same as the number of buckets which would cause there to be more files in the table directory than there are buckets.

## What is indexing and why do we need it?

Indexing is a query optimization tool in Hive that allows quicker accessing of data by a column or set of columns because Hive doesn't need to iterate over the entirety of a column to find rows that match a condition, it can simply use the index. For example, if we have a table of employees and we index the table on their salaries, if we pass the condition 'WHERE salary > 100000' then Hive can ignore every row that is below the first where salary = 100000 because the index lists those salaries in order. The disadvantage is that it takes slightly longer to write to a table where indexing is in use, because Hive has to find where in the index that new data belongs.

## SCENARIO: Suppose there are a lot of small CSV files (schema: id, name, email, country) present in the /input directory in HDFS and we want them all in a single Hive table. How should we do that?

As we should note, Hive (like HDFS) prefers to work with large datasets than multiple small ones. To that end, we want to pull all of the files into a table and output the table into a new, large, cumulitive dataset, preferably using the SequenceFile format to maintain performance. To that end, we should:
- Create a temporary table with the schema matching the CSV files:
~~~
hive>CREATE TABLE temp_table (id INT, name STRING, email STRING, country STRING)
hive>ROW FORMAT DELIMITED
hive>TERMINATED BY ','
hive>STORED AS TEXTFILE;
~~~
- Load the data into the temp_table:
~~~
hive>LOAD DATA INPATH '/input' INTO TABLE temp_table;
~~~
- Create a table that will store data in a SequenceFile format:
~~~
hive>CREATE TABLE sample_seqfile(in INT, name STRING, email STRING, country STRING)
hive>ROW FORMAT DELIMITED
hive>FIELDS TERMINATED BY ','
hive>STORED AS SEQUENCEFILE;
~~~
- Transfer the data from the temporary table into the sample_seqfile table:
~~~
hive>INSERT OVERWRITE TABLE sample SELECT * FROM temp_table;
~~~
