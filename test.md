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
* 검색 요청이 들어오면













```TEXT
- 
```

```TEXT
- 
```

```TEXT
- 
```
