# To run the instance of Kafka and Zookeeper in docker
```bash
docker compose up -d
```

# To stop Kafka and Zookeeper:
```bash
docker compose down
```

# To stop and delete all data (clean reset):
```bash
docker-compose down -v
```

NOTE: The -v ensures old data (like auto-created topics) is cleared.

# To enable auto Topic creation, comment (line: 23) in the docker-compose.yml
```bash
KAFKA_AUTO_CREATE_TOPICS_ENABLE: "false"
```

#  To run a command inside running docker
```bash
docker exec -it <container-name> <command>
```

# Create a Topic
```bash
docker exec -it kafka kafka-topics \
  --create \
  --topic my-topic \
  --bootstrap-server localhost:9092 \
  --partitions 3 \
  --replication-factor 1
```
NOTE:

--create: Tells Kafka to create a new topic.

--topic my-topic: The name of the topic.

--bootstrap-server: Connect to Kafka at this address.

--partitions: Number of partitions (controls parallelism).

--replication-factor: Number of copies of data (for fault tolerance).

# List all Topics
```bash
docker exec -it kafka kafka-topics \
  --list \
  --bootstrap-server localhost:9092
```

# Describe a Topic(details: partitions, leader, etc.)
```bash
docker exec -it kafka kafka-topics \
  --describe \
  --topic my-topic \
  --bootstrap-server localhost:9092
```

# Send Messages (Producer)
### Without Key
```bash
docker exec -it kafka kafka-console-producer \
  --topic my-topic \
  --bootstrap-server localhost:9092
```
### With Key
```bash
docker exec -it kafka kafka-console-producer \
  --topic my-topic \
  --bootstrap-server localhost:9092 \
  --property "parse.key=true" \
  --property "key.separator=:"
```

# Read Messages
### Without Key
```bash
docker exec -it kafka kafka-console-consumer \
  --topic my-topic \
  --from-beginning \
  --bootstrap-server localhost:9092
```
### With Key
```bash
docker exec -it kafka kafka-console-consumer \
  --topic my-topic \
  --bootstrap-server localhost:9092 \
  --from-beginning \
  --property print.key=true \
  --property key.separator=":"
```
### With GroupID
```bash
docker exec -it kafka kafka-console-consumer \
  --topic my-topic \
  --bootstrap-server localhost:9092 \
  --group my-consumer-group \
  --from-beginning
```

# Delete Topic
```bash
docker exec -it kafka kafka-topics \
  --delete \
  --topic my-topic \
  --bootstrap-server localhost:9092
```

# View Consumer Groups
```bash
docker exec -it kafka kafka-consumer-groups \
  --bootstrap-server localhost:9092 \
  --list
```

# Descirbe a Group
```bash
docker exec -it kafka kafka-consumer-groups \
  --bootstrap-server localhost:9092 \
  --describe \
  --group <your-consumer-group-id>
```

# Tips
- All Kafka CLI tools are located inside the Kafka container, so docker exec -it kafka is your gateway to them.

- You can also use --help with any Kafka command to learn more:
    ```bash
    docker exec -it kafka kafka-topics --help
    ```
- Kafka's default partitioning behavior changed starting with Kafka 2.4 — for the Java producer client. The behavior changed from pure round-robin to a smarter strategy called "sticky partitioning", but it's conditional.
