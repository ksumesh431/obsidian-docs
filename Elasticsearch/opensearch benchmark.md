#opensearch

in the default workloads firectory, inside geonames:

- index.json contains field names and types of the data of the index
- workload.json contains data source link which will be downloaded and used
- files.txt contains files that will be used for index



---

  
  

# Commends for testing single node config

``` bash
docker run -it -p 9200:9200 -p 9600:9600 -e OPENSEARCH_INITIAL_ADMIN_PASSWORD="L*LY8ugJfyXKVC" -e "discovery.type=single-node" --name opensearch-node opensearchproject/opensearch:2
```

<br>

  
  
  
  

# Commands for testing cluster

```bash

curl -X GET "https://localhost:9200" -ku admin:"L*LY8ugJfyXKVC"

  

curl -X GET "https://localhost:9200/_cat/nodes?v" -ku admin:"L*LY8ugJfyXKVC"

  

curl -X GET "https://localhost:9200/_cat/plugins?v" -ku admin:"L*LY8ugJfyXKVC"

  

curl -X GET "https://192.168.29.44:9200" -ku admin:'L*LY8ugJfyXKVC'

  

# Benchmark

docker run -v "$PWD/benchmarks:/opensearch-benchmark/.benchmark" \

--memory="2g" --cpus="2" \

opensearchproject/opensearch-benchmark \

execute-test \

--target-hosts https://192.168.29.44:9200 \

--pipeline benchmark-only \

--workload geonames \

--client-options basic_auth_user:admin,basic_auth_password:'L*LY8ugJfyXKVC',verify_certs:false \

--distribution-version 2.18.0 \

--test-mode

  
  

opensearch-benchmark \

execute-test \

--target-hosts https://192.168.29.44:9200 \

--pipeline benchmark-only \

--workload geonames \

--client-options basic_auth_user:admin,basic_auth_password:'L*LY8ugJfyXKVC',verify_certs:false \

--distribution-version 2.18.0 \

--test-mode

  
  

opensearch-benchmark execute-test \

--target-hosts https://192.168.29.44:9200 \

--pipeline benchmark-only \

--workload-path="/opensearch-benchmark/.benchmark/benchmarks/workloads/default/geonames/workload.json" \

--test-procedure="custom-procedure" \

--client-options basic_auth_user:admin,basic_auth_password:'L*LY8ugJfyXKVC',verify_certs:false \

--distribution-version 2.18.0 \

--test-mode  

```


---





# Custom workloads (equivalent to custom tracks on rally )
```

#################### Custom workloads creation and execution comamnds ####################
opensearch-benchmark create-workload \
--workload="custom-workload" \
--target-hosts="localhost:9200" \
--client-options="basic_auth_user:admin,basic_auth_password:cwZ9nmg2e6aWP3GSYA,verify_certs:false,timeout:120" \
--indices="bm2_pythian_dtcapi_store1_display_items2" \
--output-path="/Users/kp/Desktop/Learning/elastic_search/benchmarking/custom_workloads_testing" --offline

opensearch-benchmark execute-test \
--pipeline="benchmark-only" \
--workload-path="/Users/kp/Desktop/Learning/elastic_search/benchmarking/custom_workloads_testing/custom-workload" \
--target-hosts="localhost:9200" \
--client-options="basic_auth_user:'admin',basic_auth_password:'cwZ9nmg2e6aWP3GSYA',verify_certs:false,timeout:120"

opensearch-benchmark execute-test \
--pipeline="benchmark-only" \
--workload-path="/Users/kp/Desktop/Learning/elastic_search/benchmarking/custom_workloads_testing/custom-workload" \
--target-hosts="localhost:9200" \
--client-options="basic_auth_user:'admin',basic_auth_password:'cwZ9nmg2e6aWP3GSYA',verify_certs:false,timeout:120" \
--test-mode
```


