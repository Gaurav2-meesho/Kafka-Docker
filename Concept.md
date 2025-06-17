# Key concepts:
## Partitions
Think of a topic as a folder and partitions as separate files inside it.
Kafka decides which partition a message goes to using:
- A key (if provided)
- Otherwise: sticky/round-robin strategy

## Consumer Group
A consumer group is a set of consumers that share the work of consuming from a topic.
- Kafka assigns each partition to only one consumer in a group.
- But different groups can consume the same topic independently.

<b>Kafka guarantees: each partition is read by only one consumer in a group at a time.</b>

## Partition-to-Consumer Mapping Rules
| #Partitions | #Consumers in Group | Behavior                                               |
| ----------- | ------------------- | ------------------------------------------------------ |
| 3           | 1                   | One consumer reads from all partitions                 |
| 3           | 2                   | One gets 2 partitions, one gets 1                      |
| 3           | 3                   | One-to-one mapping                                     |
| 3           | 4                   | One partition per consumer; extra consumer is **idle** |

# 🧠Example Scenario
## Topic: user-logins (4 partitions)
👥 Consumer Group: analytics-group
| Partition | Assigned Consumer |
| --------- | ----------------- |
| 0         | consumer-1        |
| 1         | consumer-2        |
| 2         | consumer-3        |
| 3         | consumer-4        |

If consumer-2 crashes:
- Kafka reassigns partition 1 to one of the remaining consumers.
- Messages are not lost — offsets ensure safe resumption.

🔄 How Kafka Distributes Messages to Partitions
When a producer sends a message, Kafka decides which partition it should go to using the following logic:
| Key Provided? | Kafka Version | Partitioning Method         |
| ------------- | ------------- | --------------------------- |
| ✅ Yes         | Any           | Hash(key) % num\_partitions |
| ❌ No          | ≤ 2.3         | Round-robin                 |
| ❌ No          | ≥ 2.4         | Sticky partitioning         |

## Definitions:

| **Term**                   | **Explanation**                                                                             |
|---------------------------|---------------------------------------------------------------------------------------------|
| **Topic**                  | A named stream where messages are sent by producers and read by consumers.                 |
| **Partition**              | A topic is split into parts called partitions for scalability and parallelism.             |
| **Offset**                 | A unique ID (number) for each message within a partition; used for tracking.               |
| **Producer**               | A client that publishes (sends) messages to a Kafka topic.                                 |
| **Consumer**               | A client that subscribes to a topic and reads messages.                                    |
| **Consumer Group**         | A group of consumers that share work — each partition is read by one consumer in the group.|
| **Broker**                 | A Kafka server that stores data and serves client requests.                                |
| **Cluster**                | A group of Kafka brokers working together.                                                 |
| **ZooKeeper**              | (Legacy) Used for broker coordination, leader election. Now optional in Kafka 3+.          |
| **Record (Message)**       | A single unit of data sent to a Kafka topic. Consists of a key, value, and headers.        |
| **Replication**            | Copies of partitions stored on different brokers for fault tolerance.                      |
| **Leader Partition**       | One broker manages read/write operations for each partition; others follow.                |
| **ISR (In-Sync Replicas)** | Set of replicas that are up-to-date with the leader.                                       |
| **Retention Policy**       | How long Kafka stores messages (by time or size).                                          |
| **Log**                    | The actual file on disk where Kafka stores topic data — it's append-only.                  |
| **Throughput**             | The volume of data Kafka can handle per second.                                            |
| **Latency**                | Time taken for a message to be written and read.                                           |
| **Lag**                    | The number of messages a consumer group is behind from the head of the partition.          |


| **Term**                   | **Simple Meaning**                                                                         |
|----------------------------|--------------------------------------------------------------------------------------------|
| **Topic**                  | A category or channel where messages are sent (like a YouTube playlist).                   |
| **Partition**              | A part of a topic; splits the topic into smaller chunks for faster processing.             |
| **Offset**                 | A number that shows the position of a message inside a partition.                          |
| **Producer**               | The sender — it puts messages into Kafka topics.                                           |
| **Consumer**               | The receiver — it reads messages from Kafka topics.                                        |
| **Consumer Group**         | A team of consumers working together to read a topic. Each one gets a piece.               |
| **Broker**                 | A Kafka server that stores data and handles requests. Think of it like a post office.      |
| **Cluster**                | A bunch of brokers working together to make Kafka run.                                     |
| **ZooKeeper**              | A helper (used in older versions) that manages the Kafka cluster.                          |
| **Record (Message)**       | A single piece of data sent into Kafka (like one chat message).                            |
| **Replication**            | Making copies of messages so that no data is lost if something crashes.                    |
| **Leader Partition**       | The main copy of a partition where new messages go.                                        |
| **ISR (In-Sync Replicas)** | Backups that are up-to-date with the leader.                                               |
| **Retention Policy**       | How long Kafka keeps your messages before deleting them.                                   | 
| **Log**                    | A file on disk where Kafka writes the messages.                                            |
| **Throughput**             | How fast Kafka can send or receive data.                                                   |
| **Latency**                | The delay between sending and receiving a message.                                         |
| **Lag**                    | How far behind a consumer is in reading the latest messages.                               |
