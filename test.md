## Node Query Cache
* 쿼리 결과를 캐싱
* 모든 노드가 공유하는 1개의 쿼리 캐시가 있다. (쿼리용 캐시가 1개이다)
* eviction에 LRU를 씀.
* 캐싱된 데이터를 볼 수는 없다.
* 아래 설정은 클러스터 내 모든 data node에 설정되어야함.
```TEXT
- indices.queries.cache.size (default 10%)
- 5%, 512mb와 같이도 설정 가능.
```

* 아래 설정은 **index** 별로 설정 가능.
```TEXT
- index.queries.cache.enabled (default true)
- query caching enable 여부
```

## Shard request cache
* 검색 요청이 들어오면 (1개 혹은 여러 인덱스), 각 포함된 샤드는 로컬에서 검색을 실행하고, 코디네이팅 노드로 전달한다.
* 그 코디네이팅 노드는 gather 해서 global result set 만듦.
* 샤드 레벨 리퀘스트 캐시는, 각 샤드의 로컬 결과물을 캐싱함.
* 이는 빈번하거나 무거운 검색 요청을 거의 즉시 반환할 수 있게 함.
* 리퀘스트 캐시는 logging과 같은 사용성에서 좋다. 왜냐면 최근 거만 업데이트 되고, 옛날 거는 캐시에서 바로 가져올수 있으니까!

```TEXT
- 기본적으로, requests cache는 size=0인 결과만 캐싱한다.
- 그러므로, hits는 캐시하지 않지만,
- hits.total, 집계, suggestions는 캐싱을 하게된다.
```


### Cache invalidation
* 데이터가 변경된 경우에만, 샤드가 refresh될 때 캐시가 삭제된다.
* cache clear API로 만료시킬 수 있음. (링크 참조)

### Enabling and disabling caching
* 존재하는 index에 캐시 가능여부를 추가할 수 있음. (링크 참조)

### Enabling and disabling caching per request
* 쿼리 스트링 파라미터로, request_cache를 붙이면, per-request 베이스로 가능여부를 추가할 수 있다.
* 결과를 알 수 없는 script의 경우 (랜덤 등 값이 바뀌는경우) request_cache를 false로 해야한다..!

### Cache key
* 전체 json body가 키의 역할을 한다.
* json 순서가 다르면 인지하지 못한다.

### Cache settings
* 캐시는 node level에서 관리되며, default는 1% of heap 이다.
```TEXT
- indices.requests.cache.size: 1%
```

### Monitoring cache usage
* 링크 참고..

https://www.elastic.co/guide/en/elasticsearch/reference/current/shard-request-cache.html
