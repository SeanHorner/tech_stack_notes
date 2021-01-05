# MongoDB Notes

## What are the best features of MongoDB?

- **Indexing**:     It leverages indexes on document fields to improve search performance.
- **Replication**:  MongoDB runs in a distributed manner and spreads replicas out across a cluster to achieve fault-tolerence.
- **Ad-hoc Queries**: It supports ad-hoc queries by indexing BSON documents and using a unique query language. 
- **Schemaless**:   MongoDB provides excellent felxibility by not requiring documents to adhere to a schema.
- **Sharding**:     MongoDB uses sharding to allow horizontal scaling of workloads, allowing higher data throughput by leveraging cluster nodes.

## What is the default port for MongoDB?

27017

## What is a collection in MongoDB?

In MongoDB, a collection is the equivalent of a table in other database software.

## What is a document in MongoDB?

A document is the equivalent of a row in other database software.

## What rules does Mongo enforce about the structure of documents in a collection?

The only rule that MongoDB enforces is that each document needs to have a unique object id number.

## What is an object id?

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
// NOTE: if you want the _id field not to be shown, it must explicitly be declared, as above.

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

// The skip() method skips the first n documents, as specified by the skip method
db.collection.find().skip( 5 )

// These methods can be chained to get all effects consolidated into one
// The below returns 10 documents, skipping the first 5, sorting by the specified field
db.collection.find().sort({field: 1}).limit(10).skip(5)
~~~

## What are JSON?

JavaScript Object Notation, a semi-structured data format that holds all of it's data as a key-value pairs called fields.

## What data types does JSON allow?

JSON allows 6 data types: String, Number, Object, Array, Boolean, and Null.

## What are BSONs?

Binary JSON objects which have a few more datatypes than normal JSON objects, such as datetime, byte array, Double, Decimal, 32-bit Int, and 64-bit Int.

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