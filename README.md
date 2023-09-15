# elasticsearchsetup
## This is elastic seach POC
The point of this proof of concept is to set up elastic search in a multi tenancy set up. 
### What is elastic search? 
Elastic search is a document database that is powered by index inside it. It is optimized for search queries, it can e optimized by searching for the closest match, the most number of hits or both.
### What is logstash?
Logstash is a etl piplining tool which we can use to send data from our postgres to elastic search and also perform any transformations in between there.
We have to connect logstash since there is no direct way of connecting postgres to elasticsearch

### What is kibana? 
Kibana is just a dashboard for elasticsearch and logstash.

### Multirtenancy in elastic search
In elastic search for multitenancy there are two approaches
#### We can either create an index for every tenant in our postgres database.
For this we will have to set up our logstash pipeline config  a bit like this.

If our statement looks like this in the logstash config file and UserProfile has a column `tenant_id`

```
 statement => 'SELECT * FROM "UserProfile"'
```

Then we can extract that `tenant_id` and create an index on it like so. this will create a new index with the name of whatever tenant_id the userprofiles have, we can use this index to search on. In short each tenant_id will have its own index
```
output {  
    stdout {  
        codec => rubydebug   
    }
    elasticsearch {  
        hosts=> "${ELASTIC_HOSTS}"
        user=> "${ELASTIC_USER}"
        password=> "${ELASTIC_PASSWORD}"
        cacert=> "certs/ca/ca.crt"
        index       => "%{tenant_id}"
        action      => index
    }  
} 
```
We can then search this index (for example tenant_id =  1)like so: 

```
GET /1/_search
```




## OR
### We can set up alaises inside a single index for each tenant_id

## How to set up an alias? 
To set up we will have to hit the elasticsearch api with somethings like this: 

```
curl -XPOST 'http://localhost:9200/_aliases' -d '{
    "actions" : [
        {
            "add" : {
                 "index" : "users",
                 "alias" : "alias_tenant1",
                 "routing" : "1",
                 "filter" : { "term" : { "tenant_id" : "1" } }
            }
        },
        {
            "add" : {
                 "index" : "users",
                 "alias" : "alias_tenant2",
                 "routing" : "2",
                 "filter" : { "term" : { "tenant_id" : "2" } }
            }
        },
        {
            "add" : {
                 "index" : "users",
                 "alias" : "alias_tenant3",
                 "routing" : "3",
                 "filter" : { "term" : { "tenant_id" : "3" } }
            }
        }
    ]
}'
```
This create an alias for 3 tenant_ids this alias can then be searched by hitting the elastic search api
like
```
GET _alias/alias_tenant3/_search
```

## Both these apporaches have pros and cons for them at scale.
If you set up individual indexes the storage consumption increases significantly
If you set up aliases the searching time increases

#You can read more about these approaches here


https://www.elastic.co/guide/en/elasticsearch/reference/current/aliases.html

https://bigdataboutique.com/blog/multi-tenancy-with-elasticsearch-and-opensearch-c1047b?gclid=CjwKCAjw6eWnBhAKEiwADpnw9tFJ4iB4CAn82di7SC8S-bg4WzQABtGeiaQEEVju8QVwL1ndnBQgFRoChlAQAvD_BwE

https://stackoverflow.com/questions/29941296/how-to-create-an-alias-on-two-indexes-with-logstash

https://www.elastic.co/guide/en/elasticsearch/reference/current/securing-aliases.html

