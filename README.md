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
  curl -X PUT http://hostname:9200/index_name
```
Create a Pipeline in Elaticsearch to convert the date field from a string format into a data format.
```bash
curl -X PUT "hostname:9200/_ingest/pipeline/my-pipeline?pretty" -H 'Content-Type: application/json' -d'
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
curl -X PUT "hostname:9200/index_name/_settings" -H 'Content-Type: application/json' -d'
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

Once all services are [UP] we can proceed navigating to the Control Center web interface at [http://hostname:9021/](http://hostname:9021/) and select the cluster.

Install the source and sink connectors you need with the commands:
```bash
  confluent-hub install confluentinc/kafka-connect-mqtt:latest
  confluent-hub install confluentinc/kafka-connect-elasticsearch:latest
```

Create the topic you need. 

Add the Mqtt source connector to take the data in real time from the Filippetti broker and put it into the new topic. 

![App Screenshot](https://github.com/jessicapicc/elk-IoT/blob/main/image/mqtt-connector.png)

Create and write a Stream using KSQL to filter the mqtt data and create a new topic with the filtered data.

Add the Elasticsearch sink connector to send the filtered data to Elasticsearch. 

![App Screenshot](https://github.com/jessicapicc/elk-IoT/blob/main/image/elastic-connector.png)

Test if the data are in the topic with the command:
```bash
curl -XGET 'http://hostname:9200/index_name/_search?pretty'
``` 
Open Kibana at [http://hostname:5601/](http://hostname:5601/) and create the index pattern.

After selecting the index pattern you can create your custom dashboard.

![App Screenshot](https://github.com/jessicapicc/elk-IoT/blob/main/image/dashboard.png)


## Related

Here is a related project [KAFKA#IOT](https://github.com/LuciaPasseri/Kafka/tree/d58c028a9203477e56948f2291a0acb86bd05bbe)

## Feedback

If you have any feedback, please reach out to us at [jessica.piccioni@studenti.unicam.it](mailto:jessica.piccioni@studenti.unicam.it) or [giulia.morelli@studenti.unicam.it](mailto:giulia.morelli@studenti.unicam.it)

## Authors

- [Giulia Morelli](https://www.github.com/giuliamorelli)
- [Jessica Piccioni](https://github.com/jessicapicc)
