# Voxxed Days Bucharest 2022

This repository contains the demos from my session "Operate Kubernetes workloads: extend the platform with the operator pattern!" at [Voxxed Days Bucharest 2022](https://romania.voxxeddays.com/bucharest/voxxed-days-bucharest-2022/).

* [Slides](https://www.slideshare.net/paolopat/operate-kubernetes-workloads-extend-the-platform-with-the-operator-pattern-251417775)
* Video

# Requirements

A Kubernetes cluster already up and running.
If trying the demo locally, start it by using [minikube](https://github.com/kubernetes/minikube) with some minimum CPU and memory requirements.

```shell
minikube start --cpus 4 --memory 8192
```

# Install Strimzi: the Apache Kafka operator

Create a `kafka` namespace where installing the Strimzi operator.

```shell
kubectl create namespace kafka
```

Install the Strimzi operator from the provided YAML files.

```shell
kubectl create -f install/cluster-operator/ -n kafka
```

# Deploy the Apache Kafka cluster

Create the `Kafka` custom resource in order to deploy the Apache Kafka cluster.

```shell
kubectl create -f kafka-persistent.yaml -n kafka
```

# Testing via producer and consumer

Start a producer running an Apache Kafka producer to send some messages.

```shell
kubectl -n kafka run kafka-producer -ti --image=quay.io/strimzi/kafka:0.28.0-kafka-3.1.0 --rm=true --restart=Never -- bin/kafka-console-producer.sh --broker-list my-cluster-kafka-bootstrap:9092 --topic my-topic
```

Start a container running an Apache Kafka consumer to get messages.

```shell
kubectl -n kafka run kafka-consumer -ti --image=quay.io/strimzi/kafka:0.28.0-kafka-3.1.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server my-cluster-kafka-bootstrap:9092 --topic my-topic --from-beginning
```

# Making changes to the Apache Kafka cluster

Scaling the Apache Kafka cluster up, by editing the `Kafka` custom resource and changing the `replicas` field from `3` to `5`.

```shell
kubectl edit Kafka my-cluster -n kafka
```

Changing the Apache Kafka cluster brokers configuration by editing the `Kafka` custom resource and, for example, adding the `log.retention.hours: 2` in the `kafka.config` section.

```shell
kubectl edit Kafka my-cluster -n kafka
```

# Cleaning

Delete the Apache Kafka cluster.

```shell
kubectl delete -f kafka-persistent.yaml -n kafka
```

Uninstall the Strimzi operator.

```shell
kubectl delete -f install/cluster-operator/ -n kafka
```