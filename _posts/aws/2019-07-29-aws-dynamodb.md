---
layout: post
title: "[AWS] Amazon DynamoDB"
author: "Carlos Requena"
tags:
   - Amazon Web Services (AWS)
---

<span style="color:red">**"Insanity: doing the same thing over and over again and expecting different results." ― Albert Einstein**</span>

# Amazon DynamoDB

**DynamoDB**  is a truly [unique and powerful](https://brewing.codes/2017/11/06/dynamodb-overview/) database. It provides a predictable low latency, a vast array of features, tight integration with other AWS services and almost no  operational load.

**DynamoDB**  is a [NoSQL](https://brewing.codes/2017/10/30/nosql-guide/), key value/document-oriented database. As a key-value database, it allows storing an item with an id and then get an item back. As a document-oriented database, it allows storing complex nested documents.

**DynamoDB** is a serverless database, meaning that when you work with it you do not need to worry about individual machines. Instead of working with individual servers you need to specify how many read and write requests your database should process.

**DynamoDB** is a fully managed NoSQL database service that provides fast and lowlatency performance that scales with ease.

**DynamoDB** lets you offload the administrative burdens of operating a distributed NoSQL database and focus on the application.

**DynamoDB** is designed to simplify database and cluster management, provide consistently high levels of performance, simplify scalability tasks, and improve reliability with automatic replication. Developers can create a table in Amazon DynamoDB and write an unlimited number of items with consistent latency.

**DynamoDB** significantly simplifies the hardware provisioning, setup and configuration, replication, software patching, and cluster scaling of NoSQL databases.

**DynamoDB** can provide consistent performance levels by automatically distributing the data and traffic for a table over multiple partitions. After you configure a certain read or write capacity, Amazon DynamoDB will automatically add enough infrastructure capacity to support the requested throughput levels. As your demand changes over time, you can adjust the read or write capacity after a table has been created, and Amazon DynamoDB will add or remove infrastructure and adjust the internal partitioning accordingly.

> To help maintain consistent, fast performance levels, all table data is stored on highperformance SSD disk drives. Performance metrics, including transactions rates, can be monitored using Amazon CloudWatch. In addition to providing high-performance levels, DynamoDB also provides automatic high-availability and durability protections by replicating data across multiple Availability Zones within an AWS Region

## Data Model

The basic components of the Amazon DynamoDB data model include tables, items, and attributes. As depicted in image below, a table is a collection of items and each item is a collection of one or more attributes. Each item also has a primary key that uniquely identifies the item.

![]({{ site.baseurl }}/assets/images/aws/dynamodb-table.png)


- DynamoDB only requires that a table have a primary key, but it does not require you to define all of the attribute names and data types in advance.

- <span style="color:red">_Individual items in an Amazon DynamoDB table can have any number of attributes, although there is a limit of 400KB on the item size._</span>

- Each attribute in an item is a name/value pair. An attribute can be a single-valued or multivaluedset. For example, a book item can have title and authors attributes. Each book has one title but can have many authors.

- <span style="color:red">_The multi-valued attribute is a set; duplicate values are not allowed._</span>


## Data types

Unlike a traditional relational database that requires you to define your column types ahead of time, DynamoDB only requires a primary key attribute. Each item that is added to the table can
then add additional attributes. This gives you flexibility over time to expand your schema without having to rebuild the entire table and deal with record version differences with application logic.

When you create a table or a secondary index, you must specify the names and data types of each primary key attribute (partition key and sort key).

DynamoDB supports following data types:

**Scalar Data Types** A scalar type represents exactly one value. Amazon DynamoDB supports the following five scalar types:

 - **String** Text and variable length characters up to 400KB. Supports Unicode with UTF8 encoding
 - **Number** Positive or negative number with up to 38 digits of precision
 - **Binary** Binary data, images, compressed objects up to 400KB in size
 - **Boolean** Binary flag representing a true or false value
 - **Null** Represents a blank, empty, or unknown state. String, Number, Binary, Boolean cannot be empty.
 
 ```json
 {
     "video_id": "rk67v9",
     "name": "how to make pancakes",
     "featured_image": "ab4f...86ty", 
     "video_file": xxx,
     "length": 3786,
     "published": true,
     "rating": null
 }
 ```
 
**Set Data Types** Sets are useful to represent a unique list of one or more scalar values. Each value in a set needs to be unique and must be the same data type. Sets do not guarantee order. Amazon DynamoDB supports three set types: String Set, Number Set, and Binary Set.

 - **String Set** Unique list of String attributes
 - **Number Set** Unique list of Number attributes
 - **Binary Set** Unique list of Binary attributes

**Document Data Types** Document type is useful to represent multiple nested attributes, similar to the structure of a JSON file. Amazon DynamoDB supports two document types:
List and Map. Multiple Lists and Maps can be combined and nested to create complex structures.

- **List** Each List can be used to store an ordered list of attributes of different data types.
- **Map** Each Map can be used to store an unordered list of key/value pairs. Maps can be used to represent the structure of any JSON object. 

 ```json
{
    "movie_id": "123",
    "name": "star wars: the force awakens",
    "actors": [
        "daisy ridley",
        "john boyega",
    ],
    "box_office": {
        "budget": 245000000,
        "gross": 936000000
    }
}
 ```

## Primary Key

When you create a table, you must specify the primary key of the table in addition to the table name. Like a relational database, the primary key uniquely identifies each item in the table. 

A primary key will point to exactly one item. Amazon DynamoDB supports two types of primary keys, and this configuration cannot be changed after a table has been created:

 - **Partition key:** A simple primary key, composed of one attribute known as the partition key. Attributes in DynamoDB are similar in many ways to fields or columns in other database systems.
DynamoDB builds an unordered hash index on this primary key attribute.

 - **Partition key and sort key:** Referred to as a composite primary key, this type of key is composed of two attributes. The first attribute is the partition key, and the second attribute is the sort key.
Each item in the table is uniquely identified by the combination of its partition and sort key values. It is possible for two items to have the same partition key value, but those two items must have different sort key values.

Furthermore, each primary key attribute must be defined as type string, number, or binary. DynamoDB uses the partition key to distribute the request to the right partition.


 ![]({{ site.baseurl }}/assets/images/aws/dynamodb-partition-key.gif)



> Tips: If you are performing many reads or writes per second on the same primary key,
  you will not be able to fully use the compute capacity of the Amazon DynamoDB cluster.
  A best practice is to maximize your throughput by distributing requests across the full
  range of partition keys.


**Why do I need a partition key?**

 - **DynamoDB** stores data as groups of attributes, known as items. Items are similar to rows or records in other database systems. 
 - **DynamoDB** stores and retrieves each item based on the  primary key value, which must be unique. 
 - **Items** are distributed across 10-GB storage units, called partitions (physical storage internal to DynamoDB). 
 - **Each table** has one or more  partitions, as shown in the following illustration. For more information, see [Partitions and Data Distribution](https://docs.aws.amazon.com/es_es/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html) in the DynamoDB Developer Guide.
 
## Provisioned Capacity

When you create an Amazon DynamoDB table, you are required to provision a certain
amount of read and write capacity to handle your expected workloads. Based on your
configuration settings, DynamoDB will then provision the right amount of infrastructure
capacity to meet your requirements with sustained, low-latency response times. Overall
capacity is measured in read and write capacity units. These values can later be scaled up or
down by using an UpdateTable action.

Each operation against an Amazon DynamoDB table will consume some of the provisioned
capacity units. The specific amount of capacity units consumed depends largely on the size of
the item, but also on other factors. For read operations, the amount of capacity consumed
also depends on the read consistency selected in the request

When you create a table, you need to specify how many requests a table should be able to process per second. The more requests you need to process the more you will have to pay. 
Hence it pays to understand how we can use as little provisioned throughput as possible.

A table throughput is defined by two values:

 - **Number of read capacity units (RCUs)** – how many read requests can you send per second.
 - **Number of write capacity units (WCUs)** – how many write requests can you send per second.

Not all requests are equal though. The more data you read or write in a single request, the more read and write capacity will be consumed. The calculation looks like this:

 - **RCU_consumed** = ceil(size_read / 4KB)

 - **WCU_consumed** = ceil(size_written / 1KB)

You can change the provisioned throughput at any time by performing the UpdateTable API call. 
<span style="color:red">_If you send more requests to the table than it can handle DynamoDB will return the  ProvisionedThroughputExceededException exception, and you need either 
to retry or provision more capacity._</span>

For example, given a table without a local secondary index, you will consume 1 capacity unit
if you read an item that is 4KB or smaller. Similarly, for write operations you will consume 1
capacity unit if you write an item that is 1KB or smaller. This means that if you read an item
that is 110KB, you will consume 28 capacity units, or 110 / 4 = 27.5 rounded up to 28. For
read operations that are strongly consistent, they will use twice the number of capacity units,
or 56 in this example.

You can use Amazon CloudWatch to monitor your Amazon DynamoDB capacity and make
scaling decisions. There is a rich set of metrics, including ConsumedReadCapacityUnits and
ConsumedWriteCapacityUnits. If you do exceed your provisioned capacity for a period of time,
requests will be throttled and can be retried later. You can monitor and alert on the
ThrottledRequests metric using Amazon CloudWatch to notify you of changing usage
patterns.

## Secondary Indexes

W****hen you create a table with a partition and sort key (formerly known as a hash and range
key), you can optionally define one or more secondary indexes on that table. A secondary
index lets you query the data in the table using an alternate key, in addition to queries against
the primary key. DynamoDB supports two different kinds of indexes:

- **Global Secondary Index** The global secondary index is an index with a partition and sort
key that can be different from those on the table. You can create or delete a global secondary
index on a table at any time. If you create a globally secondary index for a table, you can specify an alternative simple or composite key regardless of what key type you selected 
for the table. When you write an item into the original item DynamoDB will copy data in the background into the global secondary index table.

- **Local Secondary Index** The local secondary index is an index that has the same partition
key attribute as the primary key of the table, but a different sort key. <span style="color:red">_You can only create a
local secondary index when you create a table._</span> 

Secondary indexes allow you to search a large table efficiently and avoid an expensive scan
operation to find items with specific attributes. These indexes allow you to support different
query access patterns and use cases beyond what is possible with only a primary key. <span style="color:red">_While a
table can only have one local secondary index, you can have multiple global secondary
indexes._<span>

DynamoDB updates each secondary index when an item is modified. These updates
consume write capacity units. <span style="color:red">_For a local secondary index, item updates will consume write
capacity units from the main table, while global secondary indexes maintain their own
provisioned throughput settings separate from the table._<span>

## Writing Items

Amazon DynamoDB provides three primary API actions to create, update, and delete items:
PutItem, UpdateItem, and DeleteItem. 

### PutItem

Using the **PutItem** action, you can create a new item with one or more attributes. Calls to PutItem will update an existing item if the primary key
already exists. PutItem only requires a table name and a primary key; any additional attributes are optional.

### UpdateItem

The **UpdateItem** action will find existing items based on the primary key and replace the attributes. This operation can be useful 
to only update a single attribute and leave the other attributes unchanged. UpdateItem can also be used to create items if they don’t
already exist. 

### DeleteItem

Finally, you can remove an item from a table by using **DeleteItem** and specifying a specific primary key.

## Reading Items

After an item has been created, it can be retrieved through a direct lookup by calling the
GetItem action or through a search using the Query or Scan action.

### GetItem 

**GetItem** allows you to retrieve an item based on its primary key. All of the item’s attributes are returned by default,
and you have the option to select individual attributes to filter down the results. If a primary key is composed of a partition key,
the entire partition key needs to be specified to retrieve the item. If the primary key is a composite of a partition key and a sort key,
GetItem will require both the partition and sort key as well. 

Each call to **GetItem** consumes read capacity units based on the size of the item and the consistency option selected.
By default, a GetItem operation performs an eventually consistent read. You can optionally request a strongly consistent read 
instead; this will consume additional read capacity units, but it will return the most up-to-date version of the item.

## Read Consistency

DynamoDB is available in multiple AWS Regions around the world. Each Region is independent and isolated from other AWS Regions.
For example, if you have a table called People in the us-east-2 Region and another table named People in the us-west-2 Region, these 
are considered two entirely separate tables.

Every AWS Region consists of multiple distinct locations called Availability Zones. Each Availability Zone is isolated from failures 
in other Availability Zones, and provides inexpensive, low-latency network connectivity to other Availability Zones in the same Region.
This allows rapid replication of your data among multiple Availability Zones in a Region.  

When your application writes data to a DynamoDB table and receives an HTTP 200 response (OK), the write has occurred and is durable. 
The data is eventually consistent across all storage locations, usually within one second or less. 

**DynamoDB supports eventually consistent and strongly consistent reads.** 

### Eventually Consistent Reads

When you read data from a DynamoDB table, the response might not reflect the results of a recently completed write operation. 
The response might include some stale data. If you repeat your read request after a short time, the response should return the latest 
data.

### Strongly Consistent Reads

When you request a strongly consistent read, DynamoDB returns a response with the most up-to-date data, reflecting the updates from 
all prior write operations that were successful. A strongly consistent read might not be available if there is a network delay or 
outage. <span style="color:red">_Strongly consistent reads are not supported on global secondary indexes_</span>

> Note:
DynamoDB uses eventually consistent reads, unless you specify otherwise. Read operations (such as GetItem, Query, and Scan) provide a 
ConsistentRead parameter. If you set this parameter to true, DynamoDB uses strongly consistent reads during the operation.

# Scaling and Partitioning

Amazon DynamoDB is a fully managed service that abstracts away most of the complexity
involved in building and scaling a NoSQL cluster. You can create tables that can scale up to
hold a virtually unlimited number of items with consistent low-latency performance.

An Amazon DynamoDB table can scale horizontally through the use of partitions to meet the
storage and performance requirements of your application. Each individual partition
represents a unit of compute and storage capacity. A well-designed application will take the
partition structure of a table into account to distribute read and write transactions evenly and
achieve high transaction rates at low latencies.

Amazon DynamoDB stores items for a single table across multiple partitions. DynamoDB decides which partition to store the item in 
based on the partition key. The partition key is used to distribute the new item among all of the available
partitions, and items with the same partition key will be stored on the same partition.

A partition is an allocation of storage for a table, backed by solid state drives (SSDs) and automatically replicated across multiple 
Availability Zones within an AWS Region. Partition management is handled entirely by DynamoDB—you never have to manage partitions 
yourself. 

When you create a table, the initial status of the table is CREATING. During this phase, DynamoDB allocates sufficient partitions to 
the table so that it can handle your provisioned throughput requirements. You can begin writing and reading table data after the table
status changes to ACTIVE.

DynamoDB allocates additional partitions to a table in the following situations:

- If you increase the table's provisioned throughput settings beyond what the existing partitions can support.

- If an existing partition fills to capacity and more storage space is required.

Partition management occurs automatically in the background and is transparent to your applications. Your table remains available 
throughout and fully supports your provisioned throughput requirements.

Global secondary indexes in DynamoDB are also composed of partitions. The data in a global secondary index is stored separately from 
the data in its base table, but index partitions behave in much the same way as table partitions.

As the number of items in a table grows, additional partitions can be added by splitting an
existing partition. The provisioned throughput configured for a table is also divided evenly
among the partitions. Provisioned throughput allocated to a partition is entirely dedicated to
that partition, and there is no sharing of provisioned throughput across partitions.

When a table is created, Amazon DynamoDB configures the table’s partitions based on the
desired read and write capacity. <span style="color:red">_One single partition can hold about 10GB of data and
supports a maximum of 3,000 read capacity units or 1,000 write capacity units_</span>. For partitions
that are not fully using their provisioned capacity, DynamoDB provides some burst
capacity to handle spikes in traffic. A portion of your unused capacity will be reserved to
handle bursts for short periods.

As storage or capacity requirements change, Amazon DynamoDB can split a partition to
accommodate more data or higher provisioned request rates. <span style="color:red">_After a partition is split,
however, it cannot be merged back together. Keep this in mind when planning to increase
provisioned capacity temporarily and then lower it again._</span> With each additional partition
added, its share of the provisioned capacity is reduced.

To achieve the full amount of request throughput provisioned for a table, keep your workload
spread evenly across the partition key values. Distributing requests across partition key
values distributes the requests across partitions. For example, if a table has 10,000 read
capacity units configured but all of the traffic is hitting one partition key, you will not be able
to get more than the 3,000 maximum read capacity units that one partition can support

> TIP:
To maximize Amazon DynamoDB throughput, create tables with a partition key
that has a large number of distinct values and ensure that the values are requested fairly
uniformly. Adding a random element that can be calculated or hashed is one common
technique to improve partition distribution.

>  References
>
> * [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)
> * [Data modeling with DynamoDB](https://brewing.codes/2017/11/13/dynamo-data-modeling/)
> * [Should you use DynamoDB?](https://brewing.codes/2017/11/06/dynamodb-overview/)
> * [Choosing the Right DynamoDB Partition Key](https://aws.amazon.com/es/blogs/database/choosing-the-right-dynamodb-partition-key/)




