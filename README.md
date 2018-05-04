ELK Stack
Traditional ELK setup

Setting up ELK(Elasticsearch, Logstash, Kibana) stack individually

Requirements
Host Setup
Install Docker version 17.06.0+
Install Docker Compose version 1.15.0+
Getting ElasticSearch up and running.
Download and unzip Elastic Search from official site https://www.elastic.co/downloads/elasticsearch

Run bin/elasticsearch or bin\elasticsearch.bat (on Windows)

Point your browser to http://localhost:9200 or curl http://localhost:9200/ in command line, you should get similar response.

{
"name" : "8OZ-zhg",

"cluster_name" : "elasticsearch",

"cluster_uuid" : "WyBWEwfMRDC7uf49Gy9MMA",

"version" : {

"number" : "6.1.0",

 "build_hash" : "c0c1ba0",

"build_date" : "2017-12-12T12:32:54.550Z",

"build_snapshot" : false,

 "lucene_version" : "7.1.0",

 "minimum_wire_compatibility_version" : "5.6.0",

"minimum_index_compatibility_version" : "5.0.0"

},

"tagline" : "You Know, for Search"

}
Now ElasticSearch is running on port 9200.

Getting Kibana up and running.
Download and unzip kibana from official site https://www.elastic.co/downloads/kibana .

Open config/kibana.yml in editor and set elasticsearch.url to point to your Elastic search instance.

#to do the above uncomment the line in kibana.yml;

elasticsearch.url: http://localhost:9200
Run bin/kibana (or bin\kibana.bat on Windows)

Point your browser to http://localhost:5601 .You should see Kibana Welcome page UI.

Now Kibana is running on port 5601.

Get Logstash up and running.
Download and unzip Logstach from official site https://www.elastic.co/downloads/logstash .
Running Logstach involves setting up the config file and running Logstach with that config file, which I will explain with an example.

a.	Create a logstash file logstash.config

	input { stdiin {} }

	output {

		elasticsearch {hosts => [“localhost:9200”] }

		stdout { codec => rubydebug }

		}
This is the skeleton of a logstash.config file, let’s customize it for an example and change it.

Download a kaggle data set (Cars_dataset) as an input

Customizing the config file for cars dataset.

a.	Renaming the downloaded dataset to a smaller name cars.csv

b.	Mentioning the columns names in the config files

c.	Changing the type of variables to integers using mutate (eg: mileage, price_eur etc).

d.	Specify an index name : cars , document_type : “sold_cars”

input{

file {

	path => "Path to cars.csv"

	start_position => "beginning"

	sincedb_path => "/dev/null"

	}

}

filter{

csv{

	separator => ","

	columns => ["maker", "model", "mileage", "manufacture_year", "engine_displacement", "engine_power", "body_type", "color_slug", "stk_year", "transmission", "door_count", "seat_count", "fuel_type", "date_created",   "date_last_seen", "price_eur"]
}

mutate {convert => ["mileage","integer"] }

mutate {convert => ["price_eur","float"] }

mutate {convert => ["engine_power","integer"] }

mutate {convert => ["door_count","integer"] }

mutate {convert => ["seat_count","integer"] }

}


output {

elasticsearch {

	hosts => "localhost"

	index => "cars"

	document_type => "sold_cars"

	}

	stdout {}

}
Run the following command in the logstash folder

bin/logstash –f logstash.config
Now we can visualize the dataset and create Pie charts etc in Kibana.# DockerELK
Docker ELK
