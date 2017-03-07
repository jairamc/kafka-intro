# Introduction to Kafka

---

## Disclaimer

This presentation borrows very heavily from the [Apache Kafka Intro](http://kafka.apache.org/intro) page.

note:
It is a very good intro and I highly recommend reading it

---

## My Experience

- First used Kafka 0.7.
- Last version worked on was 0.10.1.0.
- Mainly used it as
  - message buffer between different services.
  - pub-sub queue

---

## Motivation

- Must have high-throughput to support high volume event streams such as real-time log aggregation. <!-- .element: class="fragment" -->
- Must deal gracefully with large data backlogs to be able to support periodic data loads from offline systems. <!-- .element: class="fragment" -->
- Must handle low-latency delivery to handle more traditional messaging use-cases. <!-- .element: class="fragment" -->
- Must support partitioned, distributed, real-time processing of these feeds to create new, derived feeds. <!-- .element: class="fragment" -->

---

## What is Kafka?

#### Kafka is a distributed streaming platform

- It lets you publish and subscribe to streams of records. <!-- .element: class="fragment" -->
- It lets you store streams of records in a fault-tolerant way. <!-- .element: class="fragment" -->
- It lets you process streams of records as they occur. <!-- .element: class="fragment" -->

note:
- Use it as a Message Queue.
- Use it as a buffer
- Stream processor like CDC

---

## What is Kafka good for?

It gets used for two broad classes of application:

- Building real-time streaming data pipelines that reliably get data between systems or applications <!-- .element: class="fragment" -->
- Building real-time streaming applications that transform or react to the streams of data <!-- .element: class="fragment" -->

---

## Kafka APIs

![Kafka APIs](images/kafka-apis.png) <!-- .element: class="plain"-->

---

## Topics and Logs

![Log Anatomy](images/log_anatomy.png) <!-- .element height="75%" width="75%" -->

---

## Topics and Logs

- A topic is a category or feed name to which records are published.
  - Each topic has multiple partitions.
- A partition is an ordered, immutable sequence of records.
  - Records in a partition can be referenced by an offset.
- A record is a key-value pair (one can omit the key).
- All published records are retained (even if not consumed).

note:
Kafka maintains a partitioned log for each topic
Partition is a structured commit log. Ordering is guaranteed within a partition.
But there is no guarantees across partitions
There is of course a retention period
Kafka's performance is effectively constant with respect to data size so storing data for a long time is not a problem.

---

## Producers and Consumers

![Log Consumer](images/log_consumer.png)

---

## Producer

- Producers publish data to the topic of their choice.
- They choose which partition to send data to.
  - Round Robin
  - Semantic Partitioning
- Supports batching and asynchronous publish.
  - Fixed number of messages
  - Fixed latency bound

---

## Consumers

![Consumer Groups](images/consumer-groups.png)

- Each consumer instance belongs to a _consumer group_.
- Each record in a topic/partition is delivered to one consumer group.
- Two APIs:
  - High-Level - Automatic offset tracking
  - Low-Level - Explicit offset tracking

note:
If all the consumer instances have the same consumer group, then the records will effectively be load balanced over the consumer instances.
If all the consumer instances have different consumer groups, then each record will be broadcast to all the consumer processes.
Partitions are divided equally amongst the consumer instances. If instances join/leave a
consumer group, the partitions are re-divided.

---

## Message Format

```
1. 4 byte CRC32 of the message
2. 1 byte "magic" identifier to allow format changes, value is 0 or 1
3. 1 byte "attributes" identifier to allow annotations on the message independent of the version
  bit 0 ~ 2 : Compression codec.
    0 : no compression
    1 : gzip
    2 : snappy
    3 : lz4
  bit 3 : Timestamp type
    0 : create time
    1 : log append time
  bit 4 ~ 7 : reserved
4. (Optional) 8 byte timestamp only if "magic" identifier is greater than 0
5. 4 byte key length, containing length K
6. K byte key
7. 4 byte payload length, containing length V
8. V byte payload
```

---

## Distribution

- Each server in a Kafka cluster is called a _broker_.
- Each partition is replicated across a configurable number of _brokers_ for fault tolerance.
- Each partition has one server which acts as the _leader_ and zero or more servers which act as _followers_.
- The _leader_ handles all read and write requests for the partition while the _followers_ passively replicate the leader.
- If the leader fails, one of the _followers_ will automatically become the new _leader_.
- Each _broker_ acts as a _leader_ for some of its partitions and a _follower_ for others so load is well balanced within the cluster.
- Kafka uses Zookeeper for leader election.

note:
Followers simply use the consumer api like a regular consumer to consume from leaders

---

## Replication

- Replication factor can be set on a topic-by-topic basis.
- Kafka dynamically maintains a set of _in-sync replicas_ (ISR).
- On leader failure, one of the ISRs take over.
- Kafka, by default, allows unclean leader election if no ISR available.
- Producers can choose to wait for 0, 1 or all replicas to acknowledge
- Minimum ISR size can be configured. This can lead to write rejections.

note:
The unit of replication is the topic partition
Kafka doesn't use the usual `2n+1` nodes, `n+1` quorum
For `n+1` brokers with `n` ISRs, Kafka can tolerate `n` failures
`all` acknowledgement means all ISRs acknowledge, even though some replicas may be down

---

## Other useful features

- [Log compaction](http://kafka.apache.org/documentation/#compaction)
  - Only the last known value for a key is retained.
- [Quotas](http://kafka.apache.org/documentation/#design_quotas)
  - Ability to enforce quotas on produce and fetch requests.
- [Mirror Maker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker)
  - Mirror data between clusters.
- [Kafka Connect](http://kafka.apache.org/documentation/#connect)
  - Stream data between Kafka and other systems.
- [Kafka Streams](http://kafka.apache.org/0102/documentation/streams)
  - Built-in client library for processing data in Kafka as a stream
- [Authorization and Authentication](http://kafka.apache.org/documentation/#security)
  - Uses JAAS.
  - Clients using SASL (kerberos)
  - Brokers using Zookeeper
  - SSL for data between brokers

note:
Kafka Connect:  It makes it simple to quickly define connectors that move large collections of data into and out of Kafka. Kafka Connect can ingest entire databases or collect metrics from all your application servers into Kafka topics, making the data available for stream processing with low latency. An export job can deliver data from Kafka topics into secondary storage and query systems or into batch systems for offline analysis.
Kafka Streams:  It builds upon important stream processing concepts such as properly distinguishing between event time and processing time, windowing support, and simple yet efficient management of application state.
