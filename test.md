# 개요
* fielddata cache는 모든 field value를 메모리에 올린다.
* 이 때, field value는 무엇을 뜻하는가
* document 내의 field - type 값인가,
* document 내의 field 와 그 value 인가. (도큐먼트 전체와 내용과 같은..)

## Fielddata
* https://www.elastic.co/guide/en/elasticsearch/reference/7.1/modules-fielddata.html
* field data cache는 소팅이나 집계에 쓰인다.
* 모든 field values 를 메모리에 올려서, 해당 값들에 빠른 도큐먼트 기반 접근을 제공한다.
* field를 빌드하기엔 field data cache가 비싸서, 충분히 메모리를 할당하자.

## fielddata (책)(6점대)
* fielddata는 엘라스틱서치가 힙 공간에 생성하는 메모리 캐시다.
* 과거에는 많이 사용했지만, 메모리 부족 현상과 잦은 GC로 현재는 거의 사용되지 않음.
* 최신 버젼은 doc_values 라는 새로운 형태의 캐시를 제공하고있으며, text 타입의 필드를 제외한 모든 필드는 기본적으로 doc_values 캐시를 사용한다.
* fielddata를 사용해야만 하는 경우도 있다.
```TEXT
- text 타입의 필드는 기본적으로 분석기에 의해 형태소 분석이 되기 때문에 집계나 정렬 등의 기능을 수행할 수 없음.
- 부득이하게 text 타입의 필드에서 집게나 정렬을 수행할 경우, fielddata를 사용할 수 있다.
- 하지만, 메모리에 생성되는 캐시이므로 최소한으로 사용해야 한다.
```
* **3]fielddata는 메모리 소모가 크기 때문에 기본적으로 비활성화돼 있다.**

## 집계 관련
* 집계에서, 예시로 든 것이, field : movie_no , aggs - sum - field : ratings 로서,
* 결과에 대해서, **1]일치하는 "필드명" 들을 모아서 처리를(집계를) 한다.**
* 바로 이후 소개되는 캐시들 중, 필드 데이터 캐시가 나오며, 모든 필드 값을 메모리에 올리는데, 위의 일치하는 필드명 으로 보인다.


## fielddata
* https://www.elastic.co/guide/en/elasticsearch/reference/7.1/fielddata.html
* Search 는 "도큐먼트가 어떤 용어를(값을) 담고있는가" 를 묻지만,
* 소팅이나 집계는 "이 도큐먼트의 이 field의 값은 무엇인가" 를 원한다.

* 대부분의 필드는 doc_values를 이용하지만, text는 사용할 수 없다.
* 대신에, text 필드는 fielddata 라 불리는 인메모리 데이터구조를 이용한다.
* 이 자료구조는 맨 처음 필드가 집계,소팅, 스크립트에 이용될 때 만들어진다.
* 디스크 세그먼트의 전체 역색인 인덱스를 읽어서, term <-> document 관계로 전환하고, JVM heap 메모리에 저장한다.

### Fielddata는 text 필드에 대해서 default로 disable 되어있다.
* Fielddata는 많은 heap 공간을 차지하며, 특히 높은 **2] 카디널리티 텍스트 필드를 로딩할 때** 그렇다.
* heap에 한번 올라가고 나면, 세그먼트 lifetime 동안 유지된다.
* 또한 fielddata를 로딩하는 것은 매우 비싼 프로세스여서, 유저들에게 지연시간을 야기할 수 있다.



## doc_values
* https://www.elastic.co/guide/en/elasticsearch/reference/7.1/doc-values.html
* doc_values는, 역색인 구조로 인덱싱 되어서 검색에 용이하게끔 사용되는 **캐시** 이다.
* 소팅, 어그리게이션, script 내에서 field values에 접근하는것은, 다른 데이터 접근 방식이다.
* term을 찾고 documents를 찾기 보다, 도큐먼트를 look up 하고, 해당 field가 갖는 term을 찾아야 한다.

* Doc values는 on-disk 자료구조이고, document index 타임에 만들어져서 data access pattern이 가능하게 한다.
* _ _source_ 와 같은 값을 저장하지만, 정렬 및 집계에 더 효율적인 열 지향 방식으로 저장한다.
* 문서 값은 분석 된 문자열 필드를 제외하고 거의 모든 필드 유형에서 지원한다.

* 문서 값을 지원하는 모든 필드는 기본적으로 활성화 되어있다.
* 필드를 정렬하거나 집계하거나 스크립트에서 필드 값에 액세스 할 필요가없는 경우 디스크 공간을 절약하기 위해 문서 값을 비활성화 할 수 있다.

# 정리
* 여러 캐시중, doc_value 캐시는, 검색할 때 사용되는 캐시이며, text 타입의 필드를 제외한 모든 필드는 기본적으로 doc_values 캐시를 사용한다.
* text 타입의 필드는 fielddata 캐시를 사용한다.
* **1]과 2]** 를 미루어보아, 실제 데이터 (value) 값을 캐시에 올려두고, 사용하는듯 하다.
* 메모리 소모가 커서,  **3]fielddata는 메모리 소모가 크기 때문에 기본적으로 비활성화돼 있다.**
* field data cache 편에서 다룬 내용은, 메모리 할당 이야기이고, 할당은 되어있지만 기능은 비활성화 되어있다.
