## installation
http://docs.grafana.org/installation/mac/

## configuration
http://docs.grafana.org/installation/configuration/

## concepts
http://docs.grafana.org/guides/basic_concepts/

## to start
$ start_grafana_server
localhost:3000

## to pull data from es


## to use graphite query

## holt winters function
https://graphite.readthedocs.io/en/latest/functions.html#graphite.render.functions.holtWintersAberration

## the one used by TCA
https://cmsdash.rtp.raleigh.ibm.com:3000/d/stU27Xhmk/trusted-capacity-advisor-with-file-system-analytic?panelId=28&orgId=1
	click one panel -> edit -> check each query
	actual: max - metric
	predicted: moving average - max metric - options
		model: holt winters
		window: 21
		predict: 14
		alpha: 0.8
		beta: 0
		gamma: 
		period: 7

## dashboard JSON
http://docs.grafana.org/reference/dashboard/

