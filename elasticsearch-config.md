# elasticsearch.yml

## 엘라스틱 노드 명
* node.name: adp-ela-1

## 인덱스 경로
* path.data: /home1/irteam/apps/elasticsearch/data

## 로그 경로
* path.logs: /home1/irteam/logs/elasticsearch/

## 허용할 IP 목록
* network.host: 0.0.0.0

## 엘라스틱에 접근할 수 있는 HTTP API 호출 포트
* http.port: 9200

## 마스터 선출을 위한 시드 제공자 파일 설정
* discovery.seed_providers: file
```TEXT
  discovery.seed_hosts: ["호스트명", "호스트명2", ...] 
  으로도 가능하지만, 파일을 복제함으로서 설정을 간단하고 오타없이 추가할 수 있으며,
  파일이 변경되면 elasticsearch에서 자동으로 reload
```

## 초기 마스터 노드명
* cluster.initial_master_nodes: ["adp-ela-1", "adp-ela-2", "adp-ela-3"]




# unicast_hosts.txt
## 마스터 선출을 위한 시드 제공자 파일
* dev-adp-elastic001-ncl
* dev-adp-elastic002-ncl
* dev-adp-elastic003-ncl
```TEXT
  호스트가 추가되면, 파일에 추가함으로서 수정 가능
 
```
