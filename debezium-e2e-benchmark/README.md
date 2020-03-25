# Benchmark

## Benchmark existing environment (DB Server / Kafka / Connector)

The E2E benchmark is a pyton script which fill a special database table with data. One column is a timestap by insert into the table. The test compare this time with the time of the correspondig timestamp of the kafka massage in the topic. It create in the tpcdata directory the result csv and some png diagrams.

in the tpc-config.json are all function SQL stored



## Benchmark debezium-example environment

run benchmark in one docker environment CENTOS

### Build CENTOS environment

```
yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum update
yum -y install wget
yum -y install docker
yum -y install docker-compose
yum -y install maven
yum -y install git
```

## Compose DOCKER from debezium-excample

create volume directories and compose
```
mkdir /home/<user name>/dockerdata
cd /home/<user name>
git clone https://github.com/debezium/debezium-examples
cd /home/<user name>/debezium-examples/tutorial
export DEBEZIUM_VERSION=1.1  
export DEBEZIUM_DB2_VOLUME=/home/<user name>/dockerdata

docker-compose -f docker-compose-db2.yaml build

```


## run TPC debezium docker


```
cd /home/<user name>
mkdir tpcdata
chmod 777 /home/<user name>/tpcdata
export DEBEZIUM_TPC_VOLUME=/home/<user name>/tpcdata
git clone git@github.ibm.com:zrl-cogsys/eventstreams-zrl.git
cd eventstreams-zrl/src/debezium-tpc

docker-compose -f docker-compose-db2-tpc.yaml up --build
```
## KAFKA change for topic deletion

```
docker exec -it debeziumtpc_kafka_1 /bin/bash
cd /kafka
echo 'delete.topic.enable=true' >> /kafka/config/server.properties 
exit
docker stop debeziumtpc_kafka_1
docker start debeziumtpc_kafka_1
```

# run test and plots
```
docker exec -it debeziumtpc_tpc_1 /bin/bash
python3 tpc-run-test.py 
python3 runplots.py 
```
the results are in the DEBEZIUM_TPC_VOLUME


## TIPS
``` 
 /kafka/bin/kafka-topics.sh --bootstrap-server <FQDN bootstrap server>:9092 --list
 /kafka/bin/kafka-topics.sh --bootstrap-server <FQDN bootstrap server>:9092 --topic TESTDB.DB2INST1.CUSTOMERS --delete
 /kafka/bin/kafka-console-consumer.sh --bootstrap-server <FQDN bootstrap server>:9092 --topic CPRODUCER --from-beginning --property print.key=true  --property print.timestamp=true
```
