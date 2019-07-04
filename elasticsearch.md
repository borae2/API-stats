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
1. 엘라스틱서치 설정용인 elasticsearch.yml
2. JVM 설정용 jvm.options
3. logging을 위한 log4j2.properties

* 설정 파일 경로의 default는 $ES_HOME/config 이며, $ES_PATH_CONF를 변경해서 수정 가능하다.
- ES_PATH_CONF=/path/to/my/config ./bin/elasticsearch

* config 디렉토리 위치는 default가 /etc/elasticsearch이며, 이것 또한 $ES_PATH_CONF 로 수정 가능하다.

## Config file format
설정 포맷은 YAML 이다.
```TEXT
 YAML ( http://serious-code.net/doku/doku.php?id=kb:yamltutorial )
 1. 기본구조
 - 부모 자식의 구분은 들여쓰기를 통해 이루어진다.
 parent :
     childe-1 : first child
         grandchild-1 : first grand child
 2. 노드
 - 하나의 노드는 기본저긍로 key:value 페어이다.
 3. 주석
 - # 으로 주석 가능
 4. 앵커/알리아스
 - 반복되는 값은 앵커 및 알리아스를 통해 줄일 수 있다.
```

```TEXT
 path:
   data: /var/lib/elasticsearch
   logs: /var/log/elasticsearch
 
 혹은
 path.data: /var/lib/elasticsearch
 path.logs: /var/log/elasticsearch
   
```
## Environment variable substitution

```TEXT
 node.name: ${HOSTNAME}
 network.host: ${ES_NETWORK_HOST}
    
```


# Setting JVM options
JVM 설정을 건드릴 일이 잘 없지만, 한다면 heap size 변경일거다.

## Setting the heap size
* Default : 1GB (max/min)
* jvm.options를 바라보는데, Xms와 Xmx를 특히나 동일하게 셋팅을 권장.
* RAM 가용량에 따라 달라짐.

```TEXT
 - Xmx와 Xms를 50% 이상 차지하지않도록 해야한다. Elasticsearch는 JVM heap 외에도 메모리를 필요로한다.(네트워킹을 위한 off-heap buffers, files에 접근하기 위한 OS filesystem cache, JVM 자기자신)
 - JVM compressed oops threashold(대략 32GB)보다 낮게 Xmx, Xms 설정
  : heap size [1.9gb], compressed ordinary object pointers [true] 로그로 확인 가능
 - -XX:+UnlockDiagnosticVMOptions -XX:+PrintCompressedOopsMode 등의 옵션 있음... 추후 확인
 
 - jvm.options에 -Xms2g -Xmx2g 설정가능.
 - 환경변수로도 가능하다
   : ES_JAVA_OPTS="-Xms2g -Xmx2g" ./bin/elasticsearch
   : ES_JAVA_OPTS="-Xms4000m -Xmx4000m" ./bin/elasticsearch
```
## JVM Options
*  '-' 로 시작하면 JVM version과 독립적으로 취급
* 8:-Xmx2g 의 의미는, JVM이 8일때만 적용시킴.
* 8-:-Xmx2g (버젼8이상) / 8-9:-Xmx2g (범위 내의 버젼)
* 환경변수로도 설정 가능
```TEXT
 export ES_JAVA_OPTS="$ES_JAVA_OPTS -Djava.io.tmpdir=/path/to/temp/dir" ./bin/elasticsearch
```
* JAVA_OPTS를 보통 제공하지만, elasticsearch는 ***jvm.option 혹은 ES_JAVA_OPTS*** 로만 JVM 옵션을 다룬다.

# Secure Settings
# Logging configuration
# Auditing settings (X-pack)
# Cross-cluster replictaion settings (X-pack)
# Index lifecycle management settings (X-pack)

# Important Elasticsearch configuration
## path.data and path.log
* .zip, .tar.gz 등으로 설치하면, default에 중요한 폴더들이 생긴다.
* 이는 Elasticsearch를 새로운 버젼으로 업그레이드 시 삭제가 될 가능성이 크다.
* 아래와같이 변경할 수 있다.
```TEXT
 path:
  logs: /var/log/elasticsearch
  data: /var/data/elasticsearch
```
* 특히 path.data 설정은 여러개의 경로로도 가능하다.
```TEXT
path:
  data:
    - /mnt/elasticsearch_1
    - /mnt/elasticsearch_2
    - /mnt/elasticsearch_3
```

## cluster.name
* 노드는 cluster.name을 공유할 때 cluster에 속할 수 있따.
```TEXT
 cluster.name: logging-prod
```
## node.name
* elasticsearch.yml에서 변경가능
```TEXT
 node.name: prod-data-2
```
## Network setting
* network.host
```TEXT 
( https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html#network-interface-values )
- IP주소 혹은 hostname, _[networkInterface]_, _local_, _site_, _global_
- network.host: 192.168.1.10   (default==local)
```
* discovery.seed_hosts
```TEXT 
- cluster에 join하기 위해선, 적어도 몇개의 다른 클러스터 내 노드들의 IP 혹은 호스트명을 알아야한다.
- 접속할 initial node 정보를 이 옵션으로 제공
- IP, hostname 허용
- discovery.seed_hosts: ["127.0.0.1", "[::1]"]  (default)
```
* http.port
```TEXT 
- incoming HTTP request를 바인딩 할 포트.
- http.port: 9200-9300  (default)
```
* transport.port
```TEXT 
- 노드들 간 통신하기 위한 포트
- http.port: 9200-9300  (default)
```
## Discovery and cluster formation settings
클러스터 내 노드가 서로를 찾아내고, master 노드를 선출하기 위해서는 해당 셋팅이 우선되어야 한다.
### discovery.seed_hosts
* discovery.seed_hosts 는 클러스터 내 ***마스터로 선출 가능한 모든 주소***를 가져야한다.
```TEXT 
- 네트워크 구성이 없는 상태에서, elasticsearch는 가능한 loopback 주소에 바인딩하고, 9300-9305 포트를 검색해서 동일한 서버에 실행중인 노드에 연결을 시도한다.
- 이는 어떠한 설정 없이 자동으로 클러스터링을 제공한다.
- 다른 호스트에 있는 노드로 클러스터를 구성하고 싶다면, discovery.seed_hosts 를 이용해야하며, 이는 discovery process를 시드(?) 하기 위해서 살아있고 접속 가능한 master를 선출가능하게 한다. ( https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-hosts-providers.html )
- discovery.seed_hosts 는 클러스터 내 마스터로 선출 가능한 모든 주소를 가져야한다.
- 배열이나 컴마로 나열된 호스트도 가능
- host:port 나 host(transport.profiles.default.port가 기본값이며, 설정이 안되어있다면 다시 transport.port로 설정됨) 
```
### cluster.initial_master_nodes

```TEXT 
- 맨 처음 elasticsearch를 구동할 때, cluster bootstrapping step이 있으며, 이는 투표 된 matser선출 가능한 노드를 판별해준다.
- 개발 모드이며 discovery settings가 구성되지 않았을때, 이 스텝은 노드 자신들이 자동으로 수행한다.
- 이는 불안정하기에, production mode로 구동할때에는, 첫번째 선거에서 master 노드를 선출할 수 있도록 cluster.initial_master_nodes 셋팅을 해야한다.

discovery.seed_hosts:
   - 192.168.1.10:9300
   - 192.168.1.11 
   - seeds.mydomain.com 
cluster.initial_master_nodes: 
   - master-node-a
   - master-node-b
   - master-node-c
```
```TEXT 
network.host: dev-kbs-elastic001-ncl
discovery.seed_hosts: ["dev-kbs-elastic002-ncl", "dev-kbs-elastic003-ncl"]
cluster.initial_master_nodes: ["kbs-ela-1", "kbs-ela-2", "kbs-ela-3"]
```


# Clustering
## Discovery
```TEXT 
- 클러스터 형성 모듈이 클러스터 내에서 다른 노드를 찾은 과정을 discovery라 한다.
- 엘라스틱 노드가 실행될때 진행
- master node가 죽고, 새 마스터를 선출할 때
- discovery.seed_hosts 로 제공된 seed 주소들로 부터 시작된다.
- 이는 2가지 단계로 진행된다
    : 각 노드는 각 주소로 연결한 다음, 어떤 노드가 연결되었는지를 판단하여 seed 주소를 조사한다.
    : 마스터 선출가능한 모든 peer 목록을 remote 노드들과 공유하며, 원격 노드들은 자신의 동료들과 차례대로 응답한다. - 연쇄작용 하는듯.
- 찾은 노드가 master 선출가능하면, 멈추고 아니라면 계속 찾는다.
- 만약 마스터 노드를 못찾았다면, discovery.find_peers_interval (default 1) 이후 다시 시도한다.
- 노드가 마스터 선출가능하면, discovery를 계속 하는데, 선출된 마스터를 찾거나 투표를 할 수 있을 정도의 마스터 선출가능 노드들을 찾을때까지 한다. (못찾았으면 discovery.find_peers_interval (default 1) 이후 다시한다.
```
### Seed hosts providers
```TEXT 
- 클러스터 형성 모듈은 default로 2개의 seed hosts providers를 see nodes로 셋팅하도록 한다.
- discovery.seed_providers 로 셋팅된다
- 각 시드 호스트는 IP주소나 host명을 제공한다.
- 호스트명을 제공한다면, DNS lookup으로 IP를 찾게된다.
- 호스트명이 여러 IP로 구성되었다면, 모든 주소에서 seed node를 찾게된다.
- 정확한 TCP port를 주지 않는다면, 암묵적으로 port 범위중 가장 처음 port를 이용한다.(transport.profiles.default.port 또는 transport.port로 제공된)
- transport.profiles.default.port 가 제공되지 않으면, discovery.seed_resolver.max_concurrent_resolvers (10) 개의 concurrent lookup을 하며, 각 timeout은 discovery.seed_resolver.timeout (5초) 이다.
discovery.seed_hosts:
   - 192.168.1.10:9300
   - 192.168.1.11 
   - seeds.mydomain.com 

- 여러 IP라면 모두 다 연결 시도.
```
```TEXT 
- file based로 가능
 discovery.seed_providers: file
- 파일이 변경되면 Elasticsearch는 자동으로 reload해서 동적으로 설정 가능!
- 도커에서, 컨테이너가 추가돼 IP가 추가되었을때 유용하게 쓰일수있다.
- $ES_PATH_CONF/unicast_hosts.txt 에 작성
- 파일형식
10.10.10.5
10.10.10.6:9305
10.10.10.5:10005
# an IPv6 address
[2001:0db8:85a3:0000:0000:8a2e:0370:7334]:9301


- 역시나 port가 지정되지 않는다면, transport.profiles.default.port 나 transport.port 로 정해진 값으로 지정한다(port range중)
- 질문) 3대의 노드, 각각에 transport.profiles.default.port 설정 X 일 때,
   다른데서 지정해주면 그걸 쓰게될까요..? ex) 1.1.1.1:9301을 1번째 node에서 지정, 2번 node의 IP가 1.1.1.1 일때
  
```



