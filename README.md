# voxxed-days-bucharest-2022
Demo for the "Operate Kubernetes workloads: extend the platform with the operator pattern!" session at Voxxed Days Bucharest 2022


Create a `kafka` namespace where installing the Strimzi operator.

```shell
kubectl create namespace kafka
```

Install the Strimzi operator from the provided YAML files.

```shell
kubectl create -f install/cluster-operator/ -n kafka
```

Create the `Kafka` custom resource in order to deploy the Apache Kafka cluster.

```shell
kubectl create -f kafka-persistent.yaml -n kafka
```

Start consumer to test the cluster.

```shell
kubectl -n kafka run kafka-consumer -ti --image=quay.io/strimzi/kafka:0.28.0-kafka-3.1.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server my-cluster-kafka-bootstrap:9092 --topic my-topic --from-beginning
```

Start producer.

```shell
kubectl -n kafka run kafka-producer -ti --image=quay.io/strimzi/kafka:0.28.0-kafka-3.1.0 --rm=true --restart=Never -- bin/kafka-console-producer.sh --broker-list my-cluster-kafka-bootstrap:9092 --topic my-topic
```

Scaling Apache Kafka cluster up, by editing the `Kafka` custom resource and changing the `replicas` field from `3` to `5`.

```shell
kubectl edit Kafka my-cluster -n kafka
```

Changing Apache Kafka cluster brokers configuration by editing the `Kafka` custom resource and, for example, adding the `log.retention.hours: 2` in the `kafka.config` section.

```shell
kubectl edit Kafka my-cluster -n kafka
```

Delete the Apache Kafka cluster.

```shell
kubectl delete -f kafka-persistent.yaml -n kafka
```

Uninstall the Strimzi operator.

```shell
kubectl delete -f install/cluster-operator/ -n kafka
```