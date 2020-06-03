---
layout: post
title: "Apache Kafka Architecture & Fundamentals Q&A"
author: "Carlos Requena"
lang: en
ref: apache-kafka-architecture-questions
tags:
   -  "Apache Kafka"
---

<div style="text-align:center"><span style="color:red;font-weight: bold">"To copy others is necessary, but to copy oneself is pathetic" </span> <span style="color:black;font-weight: bold">--Nikola Tesla.</span></div>


# Apache Kafka Architecture & Fundamentals Q&A

**These are the answers and questions from a tech talk led by [Confluent](https://www.confluent.io/) about Apache Kafka Architecture and Fundamentals.**<object data="{{ site.baseurl }}/assets/pdf/slides-apache-kafka-architecture-fundamentals.pdf" type="application/x-pdf" title="Slides Apache Kafka Architecture and Fundamentals" width="500" height="720">
    <a href="{{ site.baseurl }}/assets/pdf/slides-apache-kafka-architecture-fundamentals.pdf">Slides Apache Kafka Architecture and Fundamentals</a> 
</object>
<br><br>

**How reliable is the data ingest to Kafka from the producer(s)?**  
Data can be acknowledged in the broker and also in copies (replicas) on other brokers. You have your choice of no ack, ack from the receiving broker, or ack once that one and all of the broker copies have successfully received the message.

<br>
**Does Kafka require Hadoop cluster infrastructure like MapReduce, HDFS, etc.?**    
No, a Kafka cluster of brokers requires only Kafka, and for now, Zookeeper

<br>
**Can we store in local storage or also on data lake storage?**     
Kafka can store on both - in memory initially and persisted on disk at an adjustable time after.

<br>
**Is the retention time the same for all brokers in a cluster? Or Can we set a different retention time for each broker?**  
Retention time can be set per topic, not per broker.

<br>
**Is there any way to keep the data rather than a generic retention time configuration setting? For example, if there is a slower moving set of topics compared to higher velocity data**   
Yes - retention times are uniquely configurable based on the particular Kafka architectural design.

<br>
**If data expires within Broker's local storage then does it mean it get lost? Suppose it has not been read/consumed by "all" the consumers of that data**  
Kafka doesn't really support the concept of a specific piece of data having been consumed. It may be consumed by multiple consumers - or if your retention period is short and 
no consumers are consuming from the topic it is in, yes a particular record could be deleted without ever being consumed.

<br>
**Kafka brokers needs a separated master?**

I'm not sure what you mean by this question. Brokers work cooperatively in a cluster. At this time, they use Zookeeper for some coordination, although that requirement will be 
removed in the future.

<br>
**Are there local disks SSD or hard drives?**   
Local disks for the broker can be of any type.

<br>
**Can some other components be used instead of Zookeeper?**     
No but Zookeeper is set to be deprecated at some point - please see KIP 500: https://www.confluent.io/blog/removing-zookeeper-dependency-in-kafka/

<br>
**Can Kafka be used in on-premise setup? If so what is the minimum system requirements/how many servers are required to configure Kafka?**  
Kafka can be run on as few as one server, but three would commonly be ideal for failover support.

<br>
**Is it kind of a layer in between source and target to stream the data directly without doing any data transformation?**   
I'm a little unclear on the intent of this question. Yes, data can pass through Kafka without any transformation. Because the producers of the data and the consumers of the data 
are decoupled, and data is persisted on the cluster, data can be consumed by multiple different consumers over time. So data could be transported to many targets.

<br>
**If a message is successfully consumed, it is stored till the retention time any way?**    
Yes! Once received, messages are immutable until the retention period expires.

<br>
**Does the data stay on local storage for 5 days ?**    
The retention time, the time that data is preserved on local storage, is configurable by topic - so data could stay on one topic for 5 days and another for a much longer or 
shorter time. It is not uncommon to have transient data - that is just being passed through Kafka, to be retained for only a few hours. Some businesses store data on some topics 
for much longer - weeks, months, years or forever - depending on their business needs.

<br>

**Is there a limit to the number of topics that a Broker node can handle?**     
There are best practices - but this is more of a logical limit, i.e., how many topics to keep track of. There needs to be enough, but not too many depending on the applications 
using Kafka.

<br>

**Is Zookeeper platformed within a Kafka instance or do I have to stand up and operate a separate zookeeper instance to keep my cluster operational?**  
Yes, Zookeeper needs to be stood up when installing Kafka currently - but as Joe said it will be going away per KIP 500. Please see: https://www.confluent.io/blog/removing-zookeeper-dependency-in-kafka/

<br>

**Can we have authorization enabled for consumer connecting using rest proxy having SASL_SSL security with SCRAM mechanism.?**  
This mechanism is for authentication - for authorization, you need to use ACLs (Access Control Lists) or the new RBAC (Role Based Access Control) which is a feature of the Confluent Platform, not open source Kafka.

<br>

**Can kafka be used as permanent data store?**  
Yes! Kafka can be used as a permanent store, ksqlDB allows it to even be used as a form of a database!

<br>

**Can we use a 3rd party enterprise metadata instead of the zookeeper?**    
At this time, Zookeeper is required. In the near future, Kafka clusters will be self managing and the Zookeeper requirement will be removed - but will not need any other 3rd party tools.

<br>

**What does adding consumers mean? Does adding consumer mean adding a new node to the cluster?**    
Adding consumers means adding more applications that are reading from the cluster (brokers).

<br>

**When kafka store data on local disk , what is preferred 1) one disk with 15tb of size or 2) 15 disks with 1tb size each?**    
OF course, that depends. Kafka is a distributed event system, so you can have multiple Kafka Brokers and then each will need to have its own local disk or disks.

<br>

**Is it common to have a consumer that is also a producer? For example, a process that takes incoming messages and "enriches" them for later consumption by a different process?**  
Yes, it is common - in fact, any stream processing application both consumes data from Kafka and then producers transformed data back to Kafka.

<br>

**If we request to produce or consume.. will it go to zookeeper or broker?**    
All clients - producers and consumers - communicate only with Kafka Brokers.

<br>

**Can a message be consumed more than once by the same consumer?**  
Generally an individual consumer will only consume any message one time - however, a consumer can set its consumer offset - the point at which it is reading from a topic - and 
could then re-read any number of messages.

<br>

**How is KAFKA different from a Message Queue MQ Series or ESB solutions?**     
The main difference is independence between the producer of messages and consumers of messages. The broker is in the middle as an independent handler of either 1) producer write or 2) 
consumer read requests in a given transaction.

<br>

**What are the data formats that are supported by Kafka? Also where does the transformations happen for business rules?**   
Kafka supports many data formats - there are many serializers of data types to include string, long, int, etc. Also there are many Schema formats to include Avro, Protobuf, 
and JSON Schema.

<br>

**Any chance of message will be lost in Kafka?**    
It is possible for a message to be lost when produced to Kafka - you should look at the producer ACK configuration settings. If a producer sets ACKS=0, then it will just send a 
message and not care if it is received by a Broker or not.

<br>

**I understand that the storage is always held by Zookeeper. Is it true or Can I set for instance Oracle as the persistence?**  
Message data in a Kafka cluster is only persisted on local Kafka Broker storage. It is not held by Zookeeper nor can you persist it to Oracle. You could, of course use Connect to 
move data from Kafka storage to Oracle if you wish.

<br>

**Any scenarios where we don't need Zookeeper or we should always use it? We have an environment implemented by consultants that does not use Zookeeper. There are also some articles 
on the Internet that talks about not using Zookeeper.**     
With the current release of Kafka, you must use Zookeeper. That requirement will be removed in a future release as Kafka clusters move to self management.

<br>

**If a broker dies, is the producer data on broker lost, or is it replicated on other brokers as well so it is never lost?**    
When you create a topic, you define the amount of replication that topic requires. It is typical to have 3 replicas for a topic - which means that even if 2 brokers crash you 
can continue to produce to and consume from your topic without data loss.

<br>

**Is retention time the only way to have data removed/dropped from the cluster or can it be set in a way that if a consumer has consumed the data then it can be dropped from the cluster?**    
Kafka doesn't keep track of whether data has been consumed or not - there can be multiple consumers doing different tasks with the same data, or one consumer may choose to re-consume 
an entire topic - so retention time controls how long data will be stored on Kafka - on a per topic basis.

<br>

**Can I add in Kafka code to enrich new data with some topic? Or need do outside in another process?**  
Yes! This is perfect for a Streams application if more than just a minimal addition is being done.

<br>

**Would it be possible to stream the messages of size 25MB via kafka? Any limitations?**    
The default maximum message size on Kafka is 1 MB. While you can configure Kafka to use larger messages, it is not optimal to do so.

<br>

**Can two producers write to the same Topic? Or is Topic specific to a Producer?**    
Yes - that is part of the beauty of Kafka! Multiple producers. And the same with consumers too! :-)

<br>

**Is the kafka architecture based generally enough to handle multiple use cases, or is the architecture very bespoke depending on each customer use case?**     
Excellent question! For basic applications, it is general enough - but for more complicated ones, that for example may need to scale, it would need to become bespoke. Rather, the 
larger the Kafka architecture the more bespoke it generally needs to be.

<br>

**What data producers will not connect to kafka?**  
To produce to Kafka, your producer needs to conform to Kafka protocols. The native APIs are Java based, but there are libraries for many other languages. If you are not able to 
use any of the available languages, you can send data to Kafka through the REST Proxy, which can communicate with any process that can send an HTTP REST request. You can also use 
Kafka Connect to move data from many standard data sources into Kafka.

<br>

**Does data also split the data in between multiple partitions?**   
A Kafka Producer will be able to send data to any partition of a topic - from the metadata it receives from a Kafka Broker, it knows where the leader broker is for each partition of 
a topic and it uses a partitioner to determine which partition to send data to.

<br>

**How to decide the partition size for a topic ? What happens if we resize the topic once it is created with "N" partitions?**  
Great question that has a lot behind it. There are some White Papers and/or blog posts on the Confluent and Kafka websites that help address this. Much of what Joe is explaining is 
the basis for that! Yes, it's possible to resize as an operation, but with some caveats.

<br>

**If I need to post transactions extracted from 500 tables into kafka, 1.is that a good idea to create 500 topics for each table, to make sure the consumers reads it in the same 
insertion order**   
Good point! Order is something that requires overhead to enforce in Kafka. So yes, you may end up needing to create individual topics to ensure order. But for what you reference, 
please check out Kafka Connect which may be able help handle that better.

<br>

**Presenter mentioned that partitions are striped across brokers, but I thought broker cluster used leader and standby replicas? Are the brokers not replicated, but working in concert?**  
Each topic may be broken into a number of partitions - you define the number when you create a topic. Those partitions can reside on different brokers - and each broker will receive 
data appropriate for its own partitions. Partitions can also be replicated - so each partition will have a leader broker and some number of follower brokers.

<br>
**Are there any advantages of using just one single topic, collecting all those 500 table messages? I am afraid this would mix up the insertion order when consumers read 500 tables 
data from one single topic.**   
Yes, to ensure order for sure you can use single topics each having single partitions. But there may be a way to design your application, depending on its needs, that could be more 
efficient - please check out the white papers and blog entries on confluent.io!

<br>

**Compaction retention policy does or does not allow for mutation of messages?**    
Messages are not mutated once they arrive at a Kafka broker - that is an inherent part of the design - all messages are immutable. However, messages can be removed from a topic if 
it is compacted - but the content of the message will not change.

<br>

**Can I change the sequences of the messages in the logs?**     
No - and yes. No you can't change what's been written to a log as it's immutable. But you can further process what's been written into a log, where you read it - and maybe 
even process it or add to it from say a DB - then write it to a different log. Pretty cool?! This is known as a Streams application.

<br>

**So, the data written by producers need to be schema less right ? And consumer needs to understand that format right?**    
The data needs to be serialized to Kafka on the way in (produce), and deserialized on the way out (consume). You can use schemas to help control this.

<br>

**How many offsets we can have in a stream?**   
The offset number for a message in a partition is a 64 bit number, so you can have 2 to the 64th power number of messages in a partition. You can define your topic to have many 
partitions. A stream can have as many messages as the underlying topic.

<br>

**Can I run Kafka on raspberry pi with small memory foot print. What is it’s memory foot print. I assuming memory foot print is configurable .please confirm.**     
Yes! Kafka will run on Raspberry Pi. Sorry I don't have the memory requirements, you should be able to find them at https://kafka.apache.org/ and please also check out 
http://confluent.io !

<br>

**Is there a chance of messages being out of sync, means the timestamp is not in order?**   
As a producer you can define what the timestamp is - you could use the event time, when the event actually took place, or the time the message is produced by the producer or the 
ingest time, when the message is written by the Broker. So, yes, you could have messages in a topic that have timestamps that are out of order.

<br>

**Is a log file equivalent to a segment? Partition has 1 to n log files**   
Raphael, yes! log file = segment.

<br>

**How to avoid multiple deliveries of same messages?**  
Simply you can turn on idempotence in your Kafka client code. You can also use Exactly Once Semantics - also known as EOS: https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/

<br>

**Can we use Kafka to process a large cuantiti of daily images, pdf documents as messages and storage?**    
Because the default message size is 1 MD in Kafka, it is not unusual to have image or pdf data directly in the messages. That said, you could chunk large messages - but then you are 
responsible for correctly reassembling them. Or, you can pass references in a message to images or pdf documents that are stored externally.

<br>

**How do you typically handle duplicate messages? Let's say we need to do some operation say write to a DB, but a consumer can receive the same message twice..And we aren't able to 
know whether the item is already written to the DB or not when we receive the duplicate msg.**  
Please check this out - explains a lot of what I think you may want to know: https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/

<br>

**Are there any limitations if we use Python ? Does streaming support Python?**     
Python is a supported language for writing Kafka producer and consumer code. However Java is the closest to Kafka's source code.
