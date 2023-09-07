# elasticsearchsetup
This is elastic seach POC 
for multitenancy there are two approaches
create an index for every tenant as displayed here

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



OR
https://www.elastic.co/guide/en/elasticsearch/reference/current/aliases.html

https://bigdataboutique.com/blog/multi-tenancy-with-elasticsearch-and-opensearch-c1047b?gclid=CjwKCAjw6eWnBhAKEiwADpnw9tFJ4iB4CAn82di7SC8S-bg4WzQABtGeiaQEEVju8QVwL1ndnBQgFRoChlAQAvD_BwE
https://stackoverflow.com/questions/29941296/how-to-create-an-alias-on-two-indexes-with-logstash


