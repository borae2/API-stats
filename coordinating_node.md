# 코디네이팅 노드 (Coordinating node)
* 검색이나 bulk indexing 요청은 다양한 data node에서 데이터를 가져온다.
* 검색 요청은 아래 2단계로 진행된다.

### 분산(scatter) 단계
* 요청을 data node로 전달한다.
* 각 data node는 로컬에서 실행되고, 결과를 코디네이팅 노드로 반환한다.

### 수집(gather) 단계
* 데이터 노드의 결과를 1개의 global resultset으로 줄인다.


### 코디네이팅 노드 특징
```TEXT
 - 모든 노드는 암묵적으로 코디네이팅 노드이다.
 - node.master, node.data, node.ingest가 false이면 코디네이팅 노드로 강제할 수 있다.
 - gather 단계를 위해서, 코디네이팅 노드는 충분한 mem과 cpu가 필요하다.
 
 - 특히나, 코디네이팅 노드는 smart load balancers 처럼 동작한다.
 - 클러스터에 join해서 full cluster state를 갖게되고, 적절한 위치로 리퀘스트를 전달한다.
 
 - 너무 많은 코디네이팅 노드를 추가하는것은 전체 클러스터에 부담이 될 수 있다. 
 - 왜냐하면 마스터 노드는 cluster state가 모든 노드로부터(코디네이팅 노드를 뜻하는듯) 업데이트 된 ack를 받아야해서!
 - 너무 과하면 안된다 - data node로도 같은 목적을 기쁘게 달성 할 수는 있긴하다.
```
https://www.elastic.co/guide/en/elasticsearch/reference/7.1/modules-node.html
