# MongoDB Notes

## What does the acronym CRUD mean in relation to databases?

Create, Retrieve, Update, Delete

## What are some CRUD methods for Mongo?

|   Acronym   | Examples               |
|:-----------:|:-----------------------|
| **C**reate  | insertOne, insertMany  |
| **R**etrieve| find                   |
| **U**pdate  | updateOne, updateMany  |
| **D**elete  | deleteOne, deleteMany  |

## What does the acronym ACID mean in relation to databases?

The acronym ACID describes the aspects that a database should have if it's practicing strict consistency. The letters stand for:
- **Atomicity**: Database transactions should be atomic, that is indivisible, and so they should fail if any part of the transaction fails. For example, if trying to update a database record, one of the update values attempts to put a string in an integer field, the entire update should fail, not just the filling of that one field.
- **Consistency**: Databases need to guarantee that the data held therein matches the rules, that is the schema, of the database so that any operations done using the data in the database won't be broken by a datatype mismatch when they go to retrieve that data. If an update is attempted with a datatype mismatch, the database should "roll back" to the previous state to ensure consistency with its rules.
- **Isolation**: Databases need to ensure that transactions happen in isolation from one another, that is that it will handle transaction requests in an orderly manner so that no two requests are competing with each other for write access to the database. An example of this would be if on e-commerce site if you are trying to buy 6 of an item and your neighbor is on the same site trying to buy 5 of the same item but there is only 10 items in stock, the site database is observing isolation if it ensures that one order goes through before the other and correctly limits the purchasing ability of the other user instead of accepting both, thereby promising more units than it has.
- **Durability**: 

## What does the acronym BASE mean in relation to databases?

- **Basically Available**:
- **Soft State**:
- **Eventual Consistency**:

## What are the best features of MongoDB?

- **Indexing**:     It leverages indexes on document fields to improve search performance.
- **Replication**:  MongoDB runs in a distributed manner and spreads replicas out across a cluster to achieve fault-tolerence.
- **Ad-hoc Queries**: It supports ad-hoc queries by indexing BSON documents and using a unique query language. 
- **Schemaless**:   MongoDB provides excellent felxibility by not requiring documents to adhere to a schema.
- **Sharding**:     MongoDB uses sharding to allow horizontal scaling of workloads, allowing higher data throughput by leveraging cluster nodes.

## What is an index in MongoDB?

An index is a listing of the documents in a collection ordered by a specified field. A collection can have multiple indices on different fields. An index is useful for increasing the speed of running a query, but the trade off is that it increases the time inserting documents into the collection takes, since Mongo has to figure out where the document fits into the index.

## What is the default port for MongoDB?

27017

## What is a collection in MongoDB?

In MongoDB, a collection is the equivalent of a table in other database software.

## What is a document in MongoDB?

A document is the equivalent of a row in other database software.

## What rules does Mongo enforce about the structure of documents in a collection?

The only rule that MongoDB enforces on documents in a collection is that each document needs to have a unique object id number.

## What is an Object ID?

An ObjectId is a unique identifier assigned to each document in MongoDB as a reference. The field for this identifier is $oid.

## What is the _id field for in MongoDB?

The _id field is another way to access the unique object id for a document.

## What is a projection in a MongoDB query?

A projection is a query 

## What does find() do?

The find() method locates documents in the collection that meet some given query criteria and some projection (i.e. which fields to return). An empty find() will return all documents with all fields showing. When describing the projection desired, it can either specify which fields are desired (by assigning them the value of 1) or it can be used to exclude fields (by assigning them the value of 0). If only exclusions are made, all other fields will be shown. If inclusions and exclusions are present in the view parameters, then the view will only include the fields marked for inclusion.

The method is used with the following syntax:
~~~
db.collection.find({query}, {projection})

// Extended query
db.collection.find({
  field1: <query value>,
  field2: <query value>
})

// Return all documents with only specified fields
db.collection.find(
  {},
  {field1: 1, field3: 1, _id: 0}
)
// NOTE: if you want the _id field not to be shown, it must explicitly be excluded, as above.

// Return all documents with all fields except those specified
db.collection.find(
  {},
  {field2: 0, field3: 0}
)
~~~

## What are some examples of filters?

Filters are shortcuts for calling specific logical operators for a query. Some examples include:
- equal to                  ($eq: value)
- not equal to              ($ne: value)
- greater than              ($gt: value)
- greater than or equal to  ($gte: value)
- included                  ($in: [list, of, values])
- not included in           ($nin: [list, of, values])
- less than                 ($lt: value)
- less than or equal to     ($lte: value)
- exists                    ($exists: boolean)

The syntax for using filters is:
~~~
{field: { $eq: { value } } }

{field: { $exists: true } }
~~~

## What do sort and limit do?

The sort() and limit() methods are used to effect the order in which results of a find operation (called a cursor) are returned. Also in this category is the skip() method.

The syntax for these methods is as follows:
~~~
// The sort() method sorts the returned documents by the listed field
db.collection.find().sort( { field: 1 } )

// The limit() method returns only as many documents as specified
db.collection.find().limit( 5 )

// The skip() method skips the first n documents, as specified in the method call
db.collection.find().skip( 5 )

// These methods can be chained to get all effects consolidated into one
// The below returns 10 documents, skipping the first 3, sorting by the specified field
db.collection.find().sort( { field: 1 } ).limit( 10 ).skip( 3 )
~~~

## What are JSON?

JavaScript Object Notation, a semi-structured data format that holds all of it's data as a key-value pairs called fields.

## What data types does JSON allow?

JSON allows 6 data types: String, Number, Object, Array, Boolean, and Null.

## What are BSONs?

Binary JSON objects which have a few more datatypes than normal JSON objects, such as datetime, byte array, Double, Decimal, 32-bit Int, and 64-bit Int.

## What is multiplicity?

Multiplicity describes how many of one object in a database can be connected to another. It comes in three variaties:
- 1-to-1
  - A 1-to-1 relationship occurs when each object can only have one corresponding object to which it is attached. For example, the relationship between a principal and their school. Each school only has one principal and each principal is responsible for only one school.
- 1-to-N
  - A 1-to-N relationship occurs when one object can be related to many others in a corresponding set, but those in the corresponding set can only have a relationship to one object in the first set. For example, the relationship between a mother and her children is 1-to-N, because one mother may have many children, but each child only has one mother. Another example is the relationship between a school and its students: a school can have many students enrolled, but each student attends only one school.
- N-to-N
  - An N-to-N relationship occurs when objects in both sets can have relationships to multiple objects in the other set. For example, the relationship between teachers and students. Each teacher is responsible for teaching many students in a class, and each student has many teachers, one for each subject.

## What is cardinality?

Cardinality is the size of the N in 1-to-N and N-to-N relationships. For example, the cardinality of the relationship between teachers and students (in the N-to-N example above) is on the order of dozens (we can say between 1-3 dozen students per class) and that the cardinality of the relationship between students and teachers is on the order of 10 (generally each student takes 6-10 classes per semester). It should be noted that cardinality is directional, in the example given which direction of the N-to-N relationship you're looking at changes the cardinality of the relationship.

## If using replication, can some members use journaling and others not?

Yes

## What is a 'namespace'?

MongoDB stores documents in collections which are grouped inside of databases. Thus the concatenation of the database name and the collection name with a dot seperator (i.e. <db>.<collection>) is called the namespace of the collection.

## When an object attribute is removed, it is deleted from the store?

Yes, you can remove an attribute and then re-save() the object.

## Are null values allowed?

Yes, any field in a BSON object can have a null value, but a null cannot be inserted into a collection since it is not an object, although an empty object (i.e. {}) can.

## Does and update write to disk immediately?

No, writes are lazy by default and updates may take a few seconds to be written to disk to allow for bundling updates together, so that the the database isn't constantly having to handle disk IO.

## How are transactions/locking done?

MongoDB doesn't use traditional locking or transactions because it is intended to be as fast and lightweight on cluster resources as possible. 

## Why are data files so large?

MongoDB uses aggressive preallocation of reserved space to avoid fragmentation of the file system.

## How long does replica set failover take?

It can take between 10-30 seconds for a primary data replica to be declared down by other cluster members and for an election to choose the new primary to take place. During this window writes and "strong consistent" reads will be down, but reads from secondary replicas is allowed.

## What is a master/primary node?

This is the node in a cluster that is responsible for all writes for its replica sets. During a failover event in a replica set, a different member is elected as primary.

## What is a secondary node?

This is a node which applies operations to keep up with the current primary. This is done by copying changes in the replication oplog (local.oplog.rs). Replication is asynchronous from the primary to the secondary, but secondaries try to stay as up-to-date as possible (often just a few milliseconds on LAN).

## Is it necessary to call 'getLastError' to make a write durable?

No, getLastError simply checks that the last write was successfully committed, it is not necessary to ensure write durability.

## Is it better to start with a sharded or non-sharded MongoDB environment?

It is better to start out with a non-sharded environment (assuming that your dataset isn't initially too large for one server) because it is more simple to operate and switching to a sharded deployment can be done wuickly and seamlessly once needed.

## How does sharding work with replication?

Each shard is a logical collection of partitioned data located either on one server or spread over a cluster. Using a replica set for each shard is highly recommended.

## What happens when a document is updated on a chunk that is being migrated?

The update will go to the old Shard first and then the change will be replicated to the new Shard before the ownership transfers.

## What happens when a Shard is down or slow when querying?

If a Shard is down, then the query will return an error (unless the 'partial' query option is set). If a shard is simply responding slowly then Mongos will wait for its response.

## Can the old files in the 'moveChunk' directory be removed?

Yes, these files are backups created during normal Shard balancing operations. Once the operations are done then they are no longer needed. Currently these files have to be cleaned up manually which should be done regularly to free up space in the database.

## How can you see the connections used by Mongos?

By using the command:
~~~
db._adminCommand("connPoolStats");
~~~

## What are some disadvantages of MongoDB?

- The 32-bit version has a 2GB limit. After that it will corrupt the entire database, including the existing data.
- MongoDB is most useful for things like analytics or caching were small amounts of data loss are acceptable. This is because by default MongoDB uses asynchronous and batch commits, which means that if there is a failure then writes in the cache could be lost.
- In MongoDB, it's difficult to represent relationships between data so you end up having to do so manually by creating extra tables to represent the relationship between rows.