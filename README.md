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



