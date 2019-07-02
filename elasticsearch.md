## 출처
https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started-concepts.html#_node

# Basic Concepts
## Near Realtime(NRT)
* 엘라스틱 서치는 거의 실시간 검색 플랫폼이다.
* 거의 1초의 매우 얕은 레이턴시로 도큐먼트의 인덱스가 검색이 가능해지도록 할 수 있다.

## Cluster
* 클러스터는 1개 이상의 노드(서버)의 집합이다. 
* 모든 데이터를 모으고, 연합 익덱싱을 제공해서 전체 노드에서 검색을 가능하게 한다.
* 유니크한 이름으로 클러스터는 구분 가능하다.(default는 elasticsearch)
* 같은 클러스터 이름은 잘못된 노드 연결을 야기한다.
* 싱글노드도 가능하다.

## Node
* 노드는, 클러스터 내 싱글 서버이며, 데이터를 저장하며, 클러스터의 인덱싱과 검색 역량에 참여한다.
* 클러스터처럼 노드도 이름으로 분류되며, default는 UUID 이다.
* 이름으로 클러스터에 연결하기때문에, 관리자에게 매우 중요하다.

## Index
* 비슷한 특성을 가진 documents의 집합체.
* customer data, produc catalog, order data 처럼 분류된 도큐먼트에 대한 index를 가질 수 있음.
* index도 이름으로 분류된다(***소문자*** 여야 한다.)
* index 이름은, indexing, 검색, 수정, 삭제 등의 연산에서 언급된다.(이용된다)

## <del>Type (Deprecated in 6.0.0) </del>
* Type은 index에 대한 논리적인 category/partition.
* 다른 타입의 document를 같은 index에 저장하도록 해줌.

## Document
* indexed 될 수 있는 기본 단위의 정보.
* document는 ***JSON***으로 표현됨
* 1개의 index에서, 원하는만큼 많은 document를 저장할 수 있다.

## Shards & Replicas
* index는 싱글 노드의 하드웨어 용량을 초과할 수 있는 많은 양의 데이터를 저장할 수 있다.
* 예를들어 10억개의 documents는 1TB의 디스크가 필요한데, 이는 싱글노드의 디스크에 맞지 않으며, 싱글노드일때에는 검색이 매우 느릴것이다.
* 이를 해결하기 위해, Elasticsearch는 index를 여러 조각으로 나누어주는데, 이를 Shards라고 한다.
* index를 만들 때, 단순히 shards의 갯수를 지정해주면 된다.
* 각각의 shard는 그 자체로 클러스터의 모든 노드에서 호스팅할 수 있는 fully-functional하고 독립적인 index이다.
* 샤드는 2가지의 이유로 중요하다 :
```TEXT
 - 볼륨을 수평적으로 분할/스케일링 할 수 있음.
 - 샤드 간 분배 및 병렬 수행을 제공하므로, 처리 성능을 향상시킨다.
```
* shard가 분배되는 방법과 document가 검색 요청에 의해 집계되는 방법은 Elasticsearch에 의해 매니징되며 사용자에게 투명하다.
* 네트워크/클라우드 환경에서 실패는 항상 일어날 수 있으므로, shard/node가 offline이 되거나 알 수 없는 이유로 데이터 유실에 대해서 failover 메커니즘을 갖는게 강력히 추천된다.
* Elasticsearch는 index의 shard를 1개 이상의 복제본을 replica shard(줄여서 replica)에 저장한다.
* 복제는 2가지 이유로 중요하다 :
```TEXT
 - shard/node 실패시 고 가용성을 제공한다. 이를위해, replica shard는 절대로 같은 original/primary shard와 같은 노드에 위치해서는 안된다.
 - 검색은 모든 replicas에서 병렬로 수행되므로, 검색 볼륨/성능을 scale out 할 수 있게 해준다.
```

* 정리하면, index는 여러 shard로 나뉘어질 수 있다.
* index는 0개(no replicas) 이상 복제 가능하다.
* 복제되면, 각각의 index는 primary shards와 replica shards로 나뉜다.
* shard와 replica의 갯수는 index가 생성될 때 정의될 수 있다.
* 이미 존재하는 index에 대한 shard의 갯수를 _shrink와 _split API로 변경할 수 있지만, 간단한 작업이 아니기 때문에 사전에 정확한 shard의 갯수를 설정하는게 베스트 프랙티스 이다.
* 클러스터에 최소한 2개의 노드라면, 기본적으로 각각의 index는 1개의 shard와 1개의 replica로 구성된다.
* 각각의 Elasticsearch shard 는 Lucene index 이기 때문에 Lucene 에서 허용하는 최대값을 따른다. [LUCENE-5843](https://issues.apache.org/jira/browse/LUCENE-5843) 을 확인해보면, 최대값은 2,147,483,519 이다.


# Installation

## Install on Linux OS
```TEXT
 - wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.2.0-linux-x86_64.tar.gz
 - tar -xzf elasticsearch-7.2.0-linux-x86_64.tar.gz
```
## Enable automatic creation of X-pack indices
* X-pack은 Elasticsearch에서 여러 지수들을 자동으로 만들어낸다.
* default로 Elasticsearch는 자동 index creation을 설정하도록 허용되어있으며, 다른 스텝은 필요없다.
* 이를 disable하려면, elasticsearch.yml에서 action.auto_create_index를 설정해야한다.
```TEXT
 - Logstach나 Beats를 이용한다면, action.auto_create_index 에 추가적인 설정이 필요하다. 하지만 정확한 환경변수를 모른다면, *로 셋팅하는것도 고려할 만 하다.
```
## Running Elasticsearch from the command line
```TEXT
 - ./bin/elasticsearch
```
## Checking that Elasticsearch is running
```TEXT
 - GET /
```
```TEXT
 {
  "name" : "Cp8oag6",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "AT69_T_DTp-1qgIJlatQqA",
  "version" : {
    "number" : "7.2.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "f27399d",
    "build_date" : "2016-03-30T09:51:41.449Z",
    "build_snapshot" : false,
    "lucene_version" : "8.0.0",
    "minimum_wire_compatibility_version" : "1.2.3",
    "minimum_index_compatibility_version" : "1.2.3"
  },
  "tagline" : "You Know, for Search"
}
```

## 기타 특이사항
```TEXT
 - daemon으로 실행
 - ./bin/elasticsearch -d -Ecluster.name=my_cluster -Enode.name=node_1 와 같이 command line으로 설정을 로드할 수 있지만,
 - cluster.name이나 node.named 을 따로 설정해서 elasticsearch.yml config 파일로 관리
 - 로그는 $ES_HOME/logs/ 에 있다. (추후 관리할때 수정하자)
```
## Directory layout of archives
| Type                   | Description                             | Default Location | Setting |
| --------------------------- |---------|---------|:-------------------------------------:|
| home | Elasticsearch home directory or $ES_HOME |Directory created by unpacking the archive| |
| bin  | Binary scripts including elasticsearch to start a node and elasticsearch-plugin to install plugins|$ES_HOME/bin||
| conf | Configuration files including elasticsearch.yml|$ES_HOME/config| ES_PATH_CONF|
| data | The location of the data files of each index / shard allocated on the node. Can hold multiple locations. |$ES_HOME/data| path.data|
| logs | Log files location. |$ES_HOME/logs| path.logs|
| plugins | Plugin files location. Each plugin will be contained in a subdirectory. |$ES_HOME/pluginse| |
| repo |Shared file system repository locations. Can hold multiple locations. A file system repository can be placed in to any subdirectory of any directory specified here. |Not configured| path.repo|
| script | Location of script files. |$ES_HOME/scripts| path.scripts|




# Configurling Elasticsearch
Elasticsearch는 기본 구성이 좋으며, 적은 구성을 요구한다. 대부분의 설정은 운영중인 클러스터에서 API로 수정될 수 있다.
```JAVA
 Cluster Update Settings
 
 GET /_cluster/settings
 
 //영구적으로 업데이트 - 재시작 이후 적용가능
 PUT /_cluster/settings
{
    "persistent" : {
        "indices.recovery.max_bytes_per_sec" : "50mb"
    }
}

// 일시적으로 업데이트
PUT /_cluster/settings?flat_settings=true
{
    "transient" : {
        "indices.recovery.max_bytes_per_sec" : "20mb"
    }
}
 
 // null로 초기화 및 wildcard 가능
 PUT /_cluster/settings
{
    "transient" : {
        "indices.recovery.*" : null
    }
}
 
```
## Order of Precedence
1. 일시적 클러스터 셋팅
2. 영구 셋팅
3. elasticsearch.yml 셋팅

* settings API로 cluster-wide하게 설정하는편이 나으며, elasticsearch.yml은 local 설정시에만 사용하도록 권유.
* 실수를 방지할 수 있으며, API는 전체 클러스터에 동일하게 적용되기 때문.


## Config files location
* 엘라스틱 서치는 3개의 설정파일이 있다.
 - 엘라스틱서치 설정용인 elasticsearch.yml
 - JVM 설정용 jvm.options
 - logging을 위한 log4j2.properties

* 설정 


## Order of Precedence





