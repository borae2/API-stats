# 클러스터 설정
## 1. Master node와 Data node
### Master node


### Data node



## 2. ingest node 설정 관련
### ingest 란?
https://www.elastic.co/guide/en/elasticsearch/reference/7.1/ingest.html

```TEXT
  실제 indexing이 일어나기 전에 document를 pre-process 하는 것.
  bulk나 index 요청을 가로채서, 변형하는 용도.
  default는 true
  사용하기 위해서는, processor들의 연속인 pipeline을 구축해야함.
  pipeline 정보는 cluster state에 저장됨.
  예를들어, 1개의 프로세서가 필드명을 바꾸고, 이후 다른 프로세서는 해당 필드를 지우도록 도 가능.
```
```TEXT
PUT my-index/_doc/my-id?pipeline=my_pipeline_id
{
  "foo": "bar"
}
```
파이프라인 예시 : https://www.elastic.co/guide/en/elasticsearch/reference/7.1/simulate-pipeline-api.html
https://www.elastic.co/guide/en/elasticsearch/reference/7.1/ingest-apis.html


### pipeline 이란?
```TEXT
 순서대로 실행되는 processor 들의 집합.
 description과 processors로 구성됨.
{
  "description" : "...",
  "processors" : [ ... ]
}

```
```TEXT
PUT _ingest/pipeline/my-pipeline-id
{
  "description" : "describe pipeline",
  "processors" : [
    {
      "set" : {
        "field": "foo",
        "value": "bar"
      }
    }
  ]
}
```
### processor 란?
 https://www.elastic.co/guide/en/elasticsearch/reference/7.1/ingest-processors.html
 * tag, on_failure, if 로 구성
 * tag : pipeline 내 프로세서의 식별자 (에러 tracing 등 프로세서 구별할때 사용)
 * if : boolean 값을 리턴하는 script를 항상 포함해야함.<br>
        script가 true이면 processor는 주어진 document에 대해 수행됨.
```TEXT
{
  "set": {
    "if": "ctx.foo == 'someValue'",
    "field": "found",
    "value": true
  }
}         
```
