To set up a watch to start sending alerts:
- Schedule the watch and define an input.
- Add a condition that checks to see if an alert needs to be sent.
- Configure an action to send an alert when the condition is met.

https://www.elastic.co/guide/en/elastic-stack-overview/current/watcher-getting-started.html

# webhook
ctx: context object
https://loopback.io/doc/en/lb2/Remote-hooks.html
https://www.elastic.co/guide/en/x-pack/current/how-watcher-works.html#watch-execution-context
https://www.elastic.co/guide/en/x-pack/current/actions-webhook.html

# check watcher status
https://www.elastic.co/guide/en/elasticsearch/reference/6.3/watcher-api-stats.html

# condition
https://www.elastic.co/guide/en/watcher/current/condition.html
https://www.elastic.co/guide/en/elastic-stack-overview/6.4/condition-script.html
https://discuss.elastic.co/t/a-watch-alert-example-based-on-two-different-searches-using-chain-input-and-painless-script-condition/113829
types of condition
	always
	never
	compare
	array_compare
	script

# action
https://www.elastic.co/guide/en/x-pack/current/actions.html

## mustache scripting
https://mustache.github.io/mustache.5.html

# watch execution context
https://www.elastic.co/guide/en/x-pack/current/how-watcher-works.html#watch-execution-context

# aggregations
https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-terms-aggregation.html
https://zaiste.net/concisely_about_aggregations_in_elasticsearch/

aggregation format: in parallel to "query" inside input-search-request-body,
"aggregations" : {
    "<aggregation_name>" : {
        "<aggregation_type>" : { 
            <aggregation_body>
        },
        ["aggregations" : { [<sub_aggregation>]* } ]
    }
    [,"<aggregation_name_2>" : { ... } ]*
}

types of aggregations: 
	merics aggregations
		computing matrices from the field’s values of the aggregated documents and sometime some values can be generated from scripts.
		cases: avg, cardinality, extended, max, min, sum.
	bucket aggregations
		cases:
			Date Histogram Aggregation
			Date Range Aggregation
			Filter Aggregation
			Filters Aggregation
			Geo Distance Aggregation
			GeoHash grid Aggregation
			Global Aggregation
			Histogram Aggregation
			IPv4 Range Aggregation
			Missing Aggregation
			Nested Aggregation
			Range Aggregation
			Reverse nested Aggregation
			Sampler Aggregation
			Significant Terms Aggregation
			Terms Aggregation
	meta aggregations
		You can add some data about the aggregation at the time of request by using meta tag and can get that in response.
		example
			> request
			{
			   "aggs" : {
			      "min_fees" : { "avg" : { "field" : "fees" } ,
			         "meta" :{
			            "dsc" :"Lowest Fees"
			         }
			      }
			   }
			}

			> response
			{
			   "aggregations":{"min_fees":{"meta":{"dsc":"Lowest Fees"}, "value":2180.0}}
			}


https://www.elastic.co/guide/en/elasticsearch/reference/master/search-aggregations-metrics-valuecount-aggregation.html

## agg by date
https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-bucket-datehistogram-aggregation.html

# examples
https://github.com/elastic/examples/tree/master/Alerting/Sample%20Watches
