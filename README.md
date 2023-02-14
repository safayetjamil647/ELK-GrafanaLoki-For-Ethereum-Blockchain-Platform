
# ELK-GrafanaLoki-For-Ethereum-Blockchain-Platform With Kafka

A full implementation of ELK and Grafana Loki for Enterprise Ethereum. There is also example with kafka integration. Hope you will learn a lot of things from this POC.  


## Author

- [Kazi Safayet Jamil](https://www.github.com/safayetjamil647)


## Acknowledgements

 - [Hyperledger Besu Ethereum](https://besu.hyperledger.org/en/stable/)
 - [ELK](https://www.elastic.co/what-is/elk-stack)
 - [Prometheus](https://prometheus.io/)
  - [Grafana](https://grafana.com/)
 - [Confluent Kafka](https://www.confluent.io/)

## Run Locally

Clone the project

```bash
  git clone https://github.com/safayetjamil647/ELK-GrafanaLoki-For-Ethereum-Blockchain-Platform
```
Go to the project directory

```bash
  cd ELK-GrafanaLoki-For-Ethereum-Blockchain-Platform
```

Run the primary project

```bash
  cd network
  sh ./start.sh
```

Stop all docker container

```bash
  sh ./stop.sh
```

Lets start digging the Elastic Stack for Enabling Log Shipping in Elastic Search using Filebeat and viusalize it with Kibana

Here the docker compose for Elasticsearch and Kibana.
```bash
x-elasticsearch-def:
  &elasticsearch-def
  image: docker.elastic.co/elasticsearch/elasticsearch:7.14.1
  environment:
    - bootstrap.memory_lock=true
    - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    - "http.host=0.0.0.0"
    - "transport.host=127.0.0.1"
    - "discovery.type=single-node"
  ulimits:
    memlock:
      soft: -1
      hard: -1
    nofile:
      soft: 65536
      hard: 65536
  cap_add:
    - IPC_LOCK

x-kibana-def:
  &kibana-def
  image: docker.elastic.co/kibana/kibana:7.14.1
  environment:
    ELASTICSEARCH_HOSTS: "http://elasticsearch:9200"
    XPACK_MONITORING_ENABLED: "true"
    XPACK_MONITORING_COLLECTION_ENABLED: "true"
    SERVER_NAME: "localhost"
  depends_on:
    - elasticsearch
  links:
    - elasticsearch
  ports:
    - 5601:5601/tcp
```
And docker compose for Filebeat

```bash
x-filebeat-def:
  &filebeat-def
  build: ./filebeat
  environment:
    - ENV_NAME=dev
    - IP_ADDRESS=127.0.0.1
    - REDIS_HOST=redis
    - REDIS_PORT=6379
  depends_on:
    - redis
  links:
    - redis
  volumes:
    - ./logs/besu/:/var/log/besu/
    - ./logs/tessera/:/var/log/tessera/
```
The file beat config file stored in filebeat directory under network directory.
also with dockerfile.That also track the logstash log files also.Just check the files for configuration .

![Elastic Search Kibana Dashboard For Logs](https://user-images.githubusercontent.com/60421249/218857749-c65dec9b-0d26-4b8b-ac0a-70c1a95248a1.png)

