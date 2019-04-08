## ES concepts
NRT 
- near realtime, a slightly latency from the time of indexing a document until the time it becomes searchable. 

cluster
- a collection of one or more nodes/servers together hold the entire data and provide federated indexing and search capabilities across all nodes. 
- identified by a unique name (default: `elasticsearch`). ex. `logging-dev`, `logging-stage`, `logging-prod`. 

node
- a single server that's part of the cluster, stores data, and involves in cluster's indexing and search capabilities. 
- identified by a name (default: random UUID assigned at startup). 
- can be configured to join a specific cluster by the cluster name. by default: nodes on the same network that can discover each other will form and join a single cluster named `elasticsearch`. 

index
- a collection of documents that have similar characteristics in a cluster. 
- identified by a name that must be all lowercase.
- operations: index, search, update, delete against the documents. 

document
- a basic unit of information that can be indexed. 
- expressed in JSON.
- stored within an index. 

shards 
- a shard is one split of an index to solve the problem when the index stores a large amount of data that exceeds the hardward limits of a single node. 
- each shard is in itself a fully functional and independent "index" that can be hosted on any node in the cluster. 
- size of shard: `_cat/shards` API; limited to the max # of docs allowed in a single Lucene index -- 128 documents. 
- sharding allows you to: the horizontally split/scale the content volume; distribute and parallelize operations across shards (potentially on multiple nodes) to increase performance/throughput. 

replica
- one or more copies of index's shards. after replication, each index will have primary shards (the original shards that were replicated from) and replica shards (the copies of the primary shards). 
- by default, each index is allocated 5 primary shards and 1 replicas. ex. 2 nodes in a cluster -> index will have 5 primary shards and another 5 replica shards (1 complete replica) for a total of 10 shards per index. 
- # of shards & replicas: can be defined at the creation of index; # of shards can be changed using `_shrink` and `_split` APIS; # of replicas can be changed dynamically at any time. 
- replication provides high availability in case a shard/node fails -- a replica shard should never be allocated on the same node as the original/primary shard that it was copied from; allows you to scale out serach volume/throughput as seaches can be executed on all replicas in parallel. 

https://www.quora.com/What-are-nodes-shards-and-clusters-in-elastic-search

## logstash concepts
https://www.elastic.co/webinars/getting-started-logstash?baymax=rtp&elektra=docs&storm=top-video&iesrc=ctr

#### a dataflow engine
- streaming ETL engine for data logistics
	inputs
		collect and deserialize data w/ input plugins. 
		codecs (a device or program that compresses data to enable faster transmission and decompresses received data) may be used for deserialization.
		inputs: Beats; TCP, UDP, HTTP; JDBC HTTP Poller. 
	filters
		struture, transform, and enrich data w/ filter plugins. 
		filters: GeoIP enrichment; external lookup enrichment; CIDR & DNS lookups. 
	outputs
		emit data to es or other destinations w/ output plugins.
		codecs may ne used for deserialization.
		outputs: es; TCP, UDP, HTTP; other stores or queues

	throughout the workflow:
		+ resilient data transport
		- at-least-once delivery guarantees and adaptive buffering w/ `persistent queues`.
		- shunt error events to the `dead letter queue` for offline introspection and replay. 
		+ pipeline dynamics
		- direct dataflow traffic w/ conditionals and multiple pipelines (ex. have these pipelines at the same time: apache pipeline, jdbc pipeline, netflow pipeline)
		- secure transport w/ authentication & wire encryption.
		- turnkey integrations w/ modules.
		- easily build custom plugin integrations and processors. 

- build dataflow pipelines
- horizontally scalable & resilient w/ adaptive buffereing
- data source agnostic
- plugin ecosystem of 200+ integrations & processors
- monitor & manage deployments w/ the elastic stack

#### logstash data sources
transform the distributed collection of data to a centralized collection one
- logs & files, metrics, security ops, message queues, cloud services, syslog servers.  
- web apps, data stores, data streams, IoT and sensors. 

#### config
- a minimal version of the config file
input {
	beats { port => 5043}
}
// implicit queue
filter {
	mutate { lowercase => ["message"] }
}
output {
	elasticsearch {}
}
- an example of logstash_ibm_stock.config
input {
	file {
		path => "/Users/Jasmine.Mou@ibm.com/Documents/business_analytics_squad/edu_sessions/09102018/IBM_stock.csv"
		start_position => "beginning"
		sincedb_path => "/dev/null"
	}
}

filter {
	csv {
		separator => ","
		columns => ['Date', 'Open', 'High', 'Low', 'Close', 'Adj Close', 'Volume']
	}
	
	mutate {
		convert => ["Open", "float"]
		convert => ["High", "float"]
		convert => ["Low", "float"]
		convert => ["Close", "float"]
		convert => ["Adj Close", "float"]
		convert => ["Volume", "integer"]
	}

	date {
		match => ["Date", "yyyy-MM-dd"]
		target => "Date"
	}
}

output {
	elasticsearch {
		action => "index"
		hosts => "localhost"
		index => "ibm_stock"
		workers => 1
	}

	stdout {}
}

#### start logstash
$ start_logstash -f PATH_TO_CONFIG_FILE/logstash_ibm_stock.config

#### event
- the primary unit of data in logstash is event, which goes thru the resources config, then thru the filters, and finally on to the applets. 
- format: documents, similar to a JSON document, w/ arbitrary hierarchies and types supported. 
{
	"@timestamp" => 2017-09-09T01-01-01,
	"message" => "bar"
	"some_other_field" => {
		"has_complex_values" => 123
	}
}

#### execution model -- pipeline
- a pipeline contains one logical flow of data: pipelines take data in through input (such as beats) -> queue (to buffer data) -> worker (to scale up the processing): mutate filter, es output. 
- multiple inputs are supported; multiple workers can handle the filter/output sections.  
- a logstash instance corresponds to a single logstash process, which may contain many pipelines that are used to separate data flows in logstash. 

#### queuing and and delivery guarantees
queue comparison
+ in memory queue (default)
- very fast
- keeps all data in memory
- not durable
- will lose in-flight data in the event of an unclean shutdown

+ persistent queue
- fast
- stores received data on disk until delivered
- durable
- will not lose data in the event of an unclean shutdown. 

at least once delivery
- not all plugins can support this but most do.
- all msgs are delivered exactly once in most conditions.
- we may run the worker (filter+output) section more than once in the event of an unclean shutdown with the PQ, which could result in a duplicate message.
- idempotent operations (always write to a specific ID) can make this functionality equivalent to exactly once. 

the dead letter queue (DLQ)
- location in the pipeline: the elasticsearch output will direct to the DLQ in the pipeline.
- some events are fundamentally undeliverable, and attempting to process them a second time is guaranteed to fail -- these items can either be dropped and logged, or sent to the DLQ.
- the DLQ can be replayed later. 


###### logstash 
- has the architecture to handle back-pressure: queuing data on disk thru persistent queues to provide at-least once delivery guarantees and buffer data locally thru ingestion spikes.
- supports integration w/ a number of dft types of message queues to allow a wide variety of deployment patterns to be supported. 
- has larger selection of plugins to: add or transform content based on lookups in config files, es, or relational db. 
- supports filtering out and dropping events based on config criteria, which is not possible in ingest node. 
- allows the use of conditionals to control flow. 
- has support for defining multiple logically separate pipelines, which can be managed thru a Kibana-based UI.
	https://www.elastic.co/guide/en/logstash/6.1/multiple-pipelines.html
	+ run more than 1 pipeline in the same process by setting up the config file `pipelines.yml` in `path.settings` folder: 
	+ using multiple pipelines is useful when: 
		-- current config has event flows that don't share the same inputs/filters/outputs.
		-- are being separated from each other using tags and conditionals.
		-- allows event flows to have different performance and durability parameters -- such separation means a blocked output in one pipeline won't exert backpressure in the other. 
	+ centralized pipeline mgmt
		https://www.elastic.co/guide/en/logstash/current/logstash-centralized-pipeline-management.html

- config file structure
input{...}
	importance of sincedb to force refreshing action:
		https://stackoverflow.com/questions/19546900/how-to-force-logstash-to-reparse-a-file/25119894#25119894
		https://www.elastic.co/guide/en/logstash/current/plugins-inputs-file.html#sincedb_path
		https://stackoverflow.com/questions/27985012/understanding-sincedb-files-from-logstash-file-input
filter{...}
output{...}



- plugin config file structure
input{
	file{
		path => "/var/log/messages"
		type => "syslog"
	}

	file{
		path => "/var/log/apache/access.log"
		type => "apache"
	}
}



#######  use logstash to inject data on ES
	https://www.youtube.com/watch?v=rKy4sFbIZ3U
	https://blog.webkid.io/visualize-datasets-with-elk/
https://finance.yahoo.com/quote/IBM/history?period1=1473480000&period2=1536552000&interval=1d&filter=history&frequency=1d
	https://www.elastic.co/guide/en/watcher/2.3/watching-time-series-data.html

####### use logstash to download data from ES
https://qbox.io/blog/how-to-export-data-elasticsearch-into-csv-file

- install plugin of logstash-input-elasticsearch
$ /Users/Jasmine.Mou@ibm.com/Applications/logstash-6.4.0/bin/logstash-plugin install logstash-input-elasticsearch 
- install plugin of logstash-output-csv
$ /Users/Jasmine.Mou@ibm.com/Applications/logstash-6.4.0/bin/logstash-plugin install logstash-output-csv
- write elasticsearch query in the input section of the logstash configuration file that will return a bunch of JSON 

input {
 elasticsearch {
    hosts => "localhost:9200"
    index => "index-we-are-reading-froml"
    query => '
  {"query": {
   .. 
#Insert your Elasticsearch query here
        }
      }
    }
}}'
  }
}
output {
  csv {
# This is the fields that you would like to output in CSV format.
# The field needs to be one of the fields shown in the output when you run your
# Elasticsearch query
    fields => ["field1", "field2", "field3","field4","field5"]
# This is where we store output. We can use several files to store our output
# by using a timestamp to determine the filename where to store output.    
path => "/tmp/csv-export.csv"
  }
}

###### Elasticsearch query DSL
scroll: 
	https://www.quora.com/How-do-I-retrieve-more-than-10000-records-in-elastic-search
	https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html

https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html
https://dzone.com/articles/23-useful-elasticsearch-example-queries


## ES reindex
#### sample script
```
POST _reindex
{
  "source": {
    "remote": {
      "host": "https://cmsdash.amm.ibmcloud.com:9200",
      "username": "fid_trusted_capacity_advisor",
      "password": "Htgmxk9BWK2LebN"
    },
    "index": "cops-tca-host-remote",
    "query": {
      "bool": {
			"must": [
				{
					"range": {
						"date": {
							"gte": "2017-11-01",
							"lte": "2018-12-31"
						}
					}
				}
			]
		}
    }
  },
  "dest": {
    "index": "cops-tca-host-local"
  }
}
```
#### cancel the reindex task
`GET _tasks?detailed=true&actions=*reindex
POST _tasks/ZY97WbezSRuJEKNoVukuYw:205672/_cancel`



## X-pack
extension for the elastic stack including: security, alerting (email, slack, jira), monitoring, reporting, graph, and machine learning (operational analytics).

## practices
#### start ES
elasticsearch -Ecluster.name=my_cluster_name -Enode.name=my_node_name

#### install ES and Kibana and logstash
https://www.elastic.co/guide/en/kibana/current/install.html
https://www.elastic.co/guide/en/kibana/6.4/connect-to-elasticsearch.html

commands added to the ./bash_profile
	$ start_elasticsearch
	$ start_kibana
	$ start_logstash

#### connect kibana to ES
Open `config/kibana.yml` in an editor. 
Set `elasticsearch.url` to point at your Elasticsearch instance. 


#### list elasticsearch plugins
https://www.elastic.co/guide/en/elasticsearch/plugins/current/listing-removing-updating.html
$ ./bin/elasticsearch-plugin list # check the currently loaded plugins
$ ./bin/elasticsearch-plugin install [pluginname]

#### data injection to ES
https://www.elastic.co/blog/should-i-use-logstash-or-elasticsearch-ingest-nodes
	ES ingest nodes vs logstash

###### ES ingest nodes
- not able to handle back-pressure: each client needs to be able to handle the case when ES is not able to keep up or accept more data when sending data to ES either directly or via an ingest pipeline. 
- has over 20 dft processors that cover the functionality of the most commonly used Logstash plugins.
- can only work in the context of a single event. 
- processors are generally not able to call out to other systems or read data from disk. 
- can be used to process and index attachments in common formats (ex. ppt, xls, pdf) while logstash cant.
- each ES ingest node pipeline is defined in a JSON document stored in ES. large number of distinct pipelines can be defined, but each doc can only be processed by a single pipeline when passing thru the ingest node. 



#### use logstash job and Kibana to viz ES
https://www.youtube.com/watch?v=imrKm6dV3NQ

####### remove ES's existing contents
$ `curl -X DELETE 'http://localhost:9200/_all'`

###### common localhosts
elasticsearch, localhost:9200
kibana, localhost:5601
logstash, localhost:9600


## Elasticsearch Watcher
https://www.elastic.co/guide/en/x-pack/current/how-watcher-works.html

watch execution context
{
	"ctx": {
		"meta": {},
		"payload": {},
		"watch_id": {},
		"execution_time": {},
		"trigger": {
			"triggered_time": ,
			"scheduled_time":
		}
	},
	"vars": {}
}
#### templates
##### stored scripts
https://www.elastic.co/guide/en/elasticsearch/reference/6.2/modules-scripting-using.html#modules-scripting-stored-scripts

##### example customized scripts 
###### to enable dynamic subjects in sent emails
{
  "actions" : {
    "email_notification" : {
      "email" : {
        "subject" : "{{ctx.metadata.color}} alert"
      }
    }
  }
}

#### how to export from elasticsearch to local file

###### method 1. logstash as csv file

check "logstash-ibm-stock-export.config"
$ start_logstash -f PATH/logstash-ibm-stock-export.config

###### method 2. curl xget w/ json query
```
$ curl -XGET "http://localhost:9200/ibm_stock/_search" -H 'Content-Type: application/json' -d '{"query": {"bool":{"must": [{"range": {"Volume":{"gte":3691300}}}]}}}' > ibm_stock.json 
```
###### method 3. curl xget w/ json file
```
curl -XGET "http://localhost:9200/ibm_stock/_search" -H 'Content-Type: application/json' -d '@ibm_stock_query_file.json' > ibm_stock.json
```
?? how to increase the scroll:

####### used by TCA
```
curl -u cops_team:us3rpa88 -k -XGET "https://cmsdash.rtp.raleigh.ibm.com:9200/cops-capacity-data/_search?scroll=1m" -H 'Content-Type: application/json' -d '@pepsi_sample_query.json' > pepsi_sample_result.json
```


#### useful commands
```
GET /_cat/indices?v    # get all indices
DELETE /INDEX?pretty
GET /.watcher-history-9-2018.12.11/_search
DELETE /.watcher-history-9-2018.12.11
```

## Aggregations
create your own script: https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-metrics-avg-aggregation.html

set the size to be 0 when only caring about the query, which avoids executing the fetch phase of the search, making the request more efficient.

## REFS
https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/returning-only-agg-results.html

https://blog.webkid.io/visualize-datasets-with-elk/
https://finance.yahoo.com/quote/IBM/history?period1=1473480000&period2=1536552000&interval=1d&filter=history&frequency=1d

## differences btw "must" and "filter" in ES query
"must" will affect the scores of the index while "filter" won't.
```
GET /_search
{
  "query": { 
    "bool": { 
      "must": [
        { "match": { "title":   "Search" }}, 
        { "match": { "content": "Elasticsearch" }}  
      ],
      "filter": [ 
        { "term":  { "status": "published" }}, 
        { "range": { "publish_date": { "gte": "2015-01-01" }}} 
      ]
    }
  }
}
```

## delete by query
```
POST /cops-capacity-data-prediction/_delete_by_query
{
  "query": {
    "bool": {
      "must": [
        {"term": {"customer.hostname": "appagpre"}}  
      ]
    }
  }
}
```

#### ES basic concepts
https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html



use logstash to load csv into elasticsearch: 

logstash job kibana visualization: 


