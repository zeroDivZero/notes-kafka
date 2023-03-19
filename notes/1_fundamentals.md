# FUNDAMENTALS

## Producer

Produces events, then Kafka manages and processes events. Helps get data into Kafka cluster. App developed by user.

![Producer](/assets/producer.png)

## Cluster

Made up of brokers, each with own processor and storage, networked and working together.

Typically not directly dealing with it much, especially if managed in cloud.

![Custer](/assets/brokers.png)

## Consumer

Reads data from cluster. Also developed by user.

![Consumer](/assets/consumer.png)

May act as producer and sends events to other consumers.

## Decoupling Producers and Consumers

* Slow consumers don't affect producers.
* Add consumers without affecting producers.
* Consumer failure doesn't affect system.

## Topic

Stream of related events. Think of log. Logical representation, categorizing events into groups.

![Topic](/assets/topic.png)

Defined by developer, no theoretical limit on number (but has practical limit on partitions). N-to-N relationship with producers.

## Partition

Topic has to persist, and broker (computer) doesn't have infinite storage and bandwidth, topic broken up into partitions stored across brokers. Key to how Kafka scales.

![Partition](/assets/partition.png)

Partition is a log. Event can only be added at end.

Implementation detail: Represented by segments, or files.

Need to think about partitioning when modeling data in topics. When topic created, cluster decides where partitions live, but size and management (such as redistributing when overloaded) up to developer. Managed cloud services typically handle those.

![Partition Example](/assets/partition-example.png)

## Log (Stream)

Time-sequenced. Can only add to end. Previous records immutable. May expire.

![Log](/assets/log.png)

Consumers only read, not actually consume and remove records. Can have multiple consumers of same log (topic). Can start reading at any offset and take as much time to catch up to most recent.

## Event Structure

![Event Structure](/assets/event-structure.png)

Key-value pairs for user-defined data. Has to have timestamp; if not provided, system time automatically added. Optional headers also key-value pairs, but are meant for meta data (like HTTP headers).

## Broker

Key function is to manage partitions. Messages of topic spread across partitions, and partitions spread across brokers. Each broker handles many partitions, all stored locally.

Takes inputs from producers and updates partitions (store). Then takes requests from consumers and outputs (pub-sub). Partition can be 1..n files. Each message in log identified by *offset*.

![Broker](/assets/broker.png)

Has configurable retention policy.

### Partition Replication

If broker down, don't want partitions down. Each partition has configurable number of replicas. Typical replication factor is 3.

![Partition Replication](/assets/partition-replication.png)

One of replicas is *leader*, others *followers*. Only produce to leaders, so producers connect to brokers with lead partitions. Brokers of follower partitions scrape from leaders (fast, less than seconds).

## Producer Development

Java is Kafka's native language, lib in Java. But also supports C/C++, Python, Go, .Net, etc. More languages by community. Can use REST Proxy if language not supported. There's also command line producer tool.

### Load Balancing and Semantic Partitioning

If message has no key, producer uses round-robin strategy (partition 0, 1, 2, ..). Partitions stay even, but no ordering.

If message has key, default strategy:

```java
hash(key) % number_of_partitions
```

Messages with same key always in same partition (as long as number of partitions constant), establishing order.

Can write custom partitioner if needed.

## Consumer Development

Same language options. Pulls messages 1..n topics. Has offset of last read per topic, new messages after that offset automatically retrieved.

Offsets stored in special topic *consumer offsets*.

Also has CLI tool.

### Consumer Group

Allows scaling by adding instances of same consumer.

![Consumer Group](/assets/consumer-group.png)
