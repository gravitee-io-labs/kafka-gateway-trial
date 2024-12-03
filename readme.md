# Prerequesites

1. Docker : if you don't you can download here : 
2. Edit your `/etc/hosts` file to map all the servers exposed as Kafka brokers form the gateway to localhost. This will not be necessary in a real environment.

Run:
```shell
sudo vim /etc/hosts
```

Then add the following line:
```
127.0.0.1       localhost kafka kafka.local broker-main0.kafka.local broker-main1.kafka.local broker-main2.kafka.local broker-main3.kafka.local broker-main4.kafka.local broker-main5.kafka.local broker-main6.kafka.local broker-main7.kafka.local broker-main8.kafka.local broker-main9.kafka.local broker-main10.kafka.local broker-main11.kafka.local broker-main12.kafka.local broker-main13.kafka.local broker-main14.kafka.local broker-main15.kafka.local broker-main16.kafka.local broker-main17.kafka.local broker-main18.kafka.local
```

Next, configure your Kafka client. There's lots of options to choose from, but for this example, we'll use the default client packaged with the official Kafka release. You can obtain the full tarball for Kafka here - https://downloads.apache.org/kafka/3.8.1/kafka_2.13-3.8.1.tgz.

Untar this to you preferred location and then set the output directly as the KAFKA_HOME variable.

```shell
export KAFKA_HOME=<path to untarred directory above>
```

# Install

From within this repository, run `docker compose up -d`. As part of this, it will install the Kafka UI running on port 8080.

# First Commands

## Listing topic

Now, if everything has succeeded thus far, you can go through the Gravitee gateway to run your list command:

```shell
$KAFKA_HOME/bin/kafka-topics.sh --bootstrap-server=kafka.local:9092 --command-config client.properties --list
```

You should see the topic `test` you created in the previous step. (You can also create the topic via the CLI).

*This is the magic!* The Kafka client is talking to the Gravitee gateway instead of the upstream cluster.

## Producing data

Now you can produce messages by starting a console consumer.

```shell
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server kafka.local:9092 --topic apidays --producer.config client.properties
```

You can type messages into the terminal and hit enter. When you're done, you can hit control-c. If you view the messages in Kafka UI, they will be there.

# Playing with the Virtual Topic policy

This is one of the many policy we have (like ACLs, Quota, ..)

Example :
```yaml
flows:
  publish: # Equivalent to PRODUCE
  subscribe: # Equivalent to FETCH
  all:
    policies:
      - id: virtual-topics
        enabled: true
        configuration:
          mappings:
            - virtual: "orders-fr"
              physical: orders
            - virtual: "orders-en"
              physical: orders
```

Have fun!



---------



# Kafka Gateway Setup and Usage Guide

This guide walks you through setting up and using the Kafka Gateway with Gravitee. By the end of this tutorial, you’ll have a functional Kafka environment integrated with Gravitee and be able to explore its features like the Virtual Topic policy.

---

## Prerequisites

### 1. Install Docker
Ensure you have Docker installed. If not, you can download it from the [official Docker website](https://www.docker.com/products/docker-desktop/).

### 2. Configure `/etc/hosts`
To map all Kafka broker servers exposed by the gateway to `localhost`, update your `/etc/hosts` file. Note: This step is unnecessary in a real environment.

Add the following line:
```txt
127.0.0.1 localhost kafka kafka.local broker-main0.kafka.local broker-main1.kafka.local broker-main2.kafka.local broker-main3.kafka.local broker-main4.kafka.local broker-main5.kafka.local broker-main6.kafka.local broker-main7.kafka.local broker-main8.kafka.local broker-main9.kafka.local broker-main10.kafka.local broker-main11.kafka.local broker-main12.kafka.local broker-main13.kafka.local broker-main14.kafka.local broker-main15.kafka.local broker-main16.kafka.local broker-main17.kafka.local broker-main18.kafka.local
```

### 3. Install Kafka
Download [Apache Kafka 3.8.1](https://downloads.apache.org/kafka/3.8.1/kafka_2.13-3.8.1.tgz):

```shell
wget https://downloads.apache.org/kafka/3.8.1/kafka_2.13-3.8.1.tgz
tar -xvzf kafka_2.13-3.8.1.tgz
```

Set the Kafka home directory:
```shell
export KAFKA_HOME=<path-to-untarred-kafka-directory>
```

---

## Installation

To set up the environment, run:
```shell
docker-compose up -d
```

This command:
- Installs and configures the Kafka Gateway.
- Launches Kafka UI, accessible at `http://localhost:8080`. You can create a new topic `test` for example.

---

## First Commands

### 1. List Topics
Verify your setup by listing topics through the Gravitee gateway:
```shell
$KAFKA_HOME/bin/kafka-topics.sh --bootstrap-server=kafka.local:9092 --command-config client.properties --list
```

You should see the topic `test` if created successfully.

### 2. Produce Data
Produce messages using the console producer:
```shell
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server kafka.local:9092 --topic apidays --producer.config client.properties
```

Type messages in the terminal and press Enter. Check the Kafka UI to see your messages.

---

## Virtual Topic Policy

The Virtual Topic policy is one of many policies available in Gravitee (e.g., ACLs, Quota, etc.).

### Example Configuration:
```yaml
flows:
  publish: # Equivalent to PRODUCE
  subscribe: # Equivalent to FETCH
  all:
    policies:
      - id: virtual-topics
        enabled: true
        configuration:
          mappings:
            - virtual: "orders-fr"
              physical: orders
            - virtual: "orders-en"
              physical: orders
```

This configuration allows mapping virtual topics `orders-fr` and `orders-en` to a physical topic `orders`.

### 2. Produce Data
Produce messages using the console producer:
```shell
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server kafka.local:9092 --topic orders-fr --producer.config client.properties
```

---

## Troubleshooting

### Common Issues
- **Docker not starting:** Ensure Docker is installed and running on your machine.
- **Kafka client not working:** Double-check the `/etc/hosts` configuration and ensure the `client.properties` file is correctly set up.

### Logs
Check Docker logs for more insights:
```shell
docker logs <container_name>
```

---

## Have Fun!

Explore Gravitee’s features and enjoy seamless Kafka integration!
