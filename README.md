# Data Analytics on Elasticsearch for IoT

Data Analytics on Elastichsearch - ELK#IOT

The objective of the project is to create a set of IoT data visualization dashboards based on Elasticsearch ([https://www.elastic.co/](https://www.elastic.co/)). The data to be monitored will be IoT sensors installed in the Computer Science department and classrooms of Camerino University. Initially the IoT data will consider the ones from [Filippetti](https://www.filippetti.it/) sensors.
Data must be ingested with Kafka and sent in real time to Elasticsearch and displayed on one or more visualization dashboards defined in Kibana. 

## 🛠 Prerequisites
- Ubuntu 21.04 64 bit
- Java 11.0
- Docker 20.10
- PuTTY 0.75 64 bit

## Installation
- [Elasticsearch 7.10](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
- [Kibana 7.10](https://codingfundas.com/how-to-install-elasticsearch-7-with-kibana-using-docker-compose/index.html)
- [Confluent Platform 6.2](https://docs.confluent.io/platform/current/quickstart/ce-quickstart.html)

## Usage

Start Elasticsearch and Kibana with docker-compose.
```bash
  docker-compose up 
```
Create an index in Elasticsearch.
```bash
  curl -X PUT http://localhost:9200/index_name
```
Create a Pipeline in Elaticsearch to convert the datafield from a string format into a data format.
```bash
curl -X PUT "localhost:9200/_ingest/pipeline/my-pipeline?pretty" -H 'Content-Type: application/json' -d'
{
  "description" : "...",
  "processors" : [
    {
      "date" : {
        "field" : "TZ",
        "formats" : ["date_optional_time"],
        "timezone" : "Europe/London"
      }
    }
  ]
}
'
```
Set the pipeline as default pipeline for the index.
```bash
curl -X PUT "localhost:9200/index_name/_settings" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index.default_pipeline": "my-pipeline"
  }
}
'
```
Launch Confluent from any directory with the underneath command:

```bash
  confluent local services start
```

Navigate to the Control Center web interface at [http://localhost:9021/](http://localhost:9021/) and select your cluster

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

Install the source and sink connectors you need with the command
```bash
  confluent-hub install confluentinc/kafka-connect-mqtt:latest
  confluent-hub install confluentinc/kafka-connect-elasticsearch:latest
```

Create the topic you need. 

Add the Mqtt source connector to take the data in real time from the Filippetti broker and put it into the topic that you defined. 

Create and Write a Stream using KSQL to filter the mqtt data and create a new topic with the filtered data

Add the Elasticsearch sink connector to pass the data from the topic where there are the filtered data to Elasticsearch. 

Test if the data are in the topic with the command:
```bash
curl -XGET 'http://localhost:9200/index_name/_search?pretty'
``` 
Open Kibana at [http://localhost:5601/](http://localhost:5601/) and create the dashboard. 

## Related

Here is a related project [KAFKA#IOT](https://github.com/LuciaPasseri/Kafka/tree/d58c028a9203477e56948f2291a0acb86bd05bbe)

## Feedback

If you have any feedback, please reach out to us at [jessica.piccioni@studenti.unicam.it](mailto:jessica.piccioni@studenti.unicam.it) or [giulia.morelli@studenti.unicam.it](mailto:giulia.morelli@studenti.unicam.it)

## Authors

- [Giulia Morelli](https://www.github.com/giuliamorelli)
- [Jessica Piccioni](https://github.com/jessicapicc)
