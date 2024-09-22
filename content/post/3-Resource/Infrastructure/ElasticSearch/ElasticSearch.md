---
date: 2023-10-05T23:26:11
updatedAt: 2024-06-23 20:56:34
tags:
  - ElasticSearch
  - hugo_blog
categories:
  - ElasticSearch
title: ElasticSearch
lastmod: 2024-06-23T11:56:34.327Z
menu:
  main:
    name: title (optional)
    weight: -90
    params:
      icon: icon-name
---
* 분산 문서 장소
* 분산 시스템으로 구성되어 대용량 데이터와 빠른 검색 가능
* 역인덱스구조

## Document

* 엘라스틱 서치의 단일 데이터 단위
* JSON구조로 되어있음
* 디폴트로 schema-less구조
  * schema를 지정 하지 않아도 됨
  * schema를 지정할 수 있으며, schema를 지정했다면, 모든 document들은 schema를 따라야함
* metadata
  * \_index document가 저장된 index를 나타냄
  * \_id doucument의 id를 나타냄

## Node

* 하나의 ElasticSearch 인스턴스
* 하나이상의 Node가 모여 Cluster를 구성
* node는 여러 role을 가질 수 있음
  * master, data, data\_content, data\_hot, data\_warm, data\_cold, data\_frozen, ingest, ml, remote\_cluster\_client, transform

### Master node

* 인덱스의 생성 또는 삭제, 클러스터의 일부인 노드추적, 어떤 샤드를 어떤 노드를 할당할지 결정하는 등 가벼운 작업을 담당

### Data node

* 인덱싱한 문서가 포함된 샤드를 보관
* CRUD, 검색, 집계와 같은 데이터 관련 작업 처리
  * I/O, 메모리, CPU 집약적인 작업
* hot, warm, cold 등 여러 data노드들이 있음

## Index

* 유사한 특성을 가진 문서들의 모음
* es에서 쿼리할 수 있는 가장 높은 수준의 엔티티

## Shard

* 하나의 인덱스는 하나 이상의 shard로 분리되어 저장됨
* 인덱스를 구성하는 기본단위
* Lucene의 인스턴스이며, 그 자체로 완벽한 검색엔진임
* document가 저장되어있고
* primary shard와 replica shard가 존재함
  * primary shard
    * 각각의 document는 하나의 primary shard에 속함
    * 최대 Integer.MAX\_VALUE - 128 만큼의 document를 저장할 수 있음
    * update시 즉시 업데이트
  * replica shard
    * primary shard의 복제본
    * update시 즉시 업데이트 되지 않음
    * 장애 복구 시 및 검색시 사용함
  * primary샤드수를 바꾸려면 reindexing해야함

## 역인덱스(inverted index)

* 해당 단어가 나타내는 문서들 간의 매핑을 제공해주는 데이터 구조
* 각 단어가 어떤 문서들에 등장하는지 알려줌
* inverted index와 forward index는 방향성의 차이임

```jsx
#forward index
Document                          Keywords
doc1                              hello, sky, morning      
doc2                              tea, coffee, hi
doc3                              greetings, sky
```

```jsx
#inverted index
Word                              Documents
hello                             doc1      
sky                               doc1, doc3
coffee                            doc2
hi                                doc2
greetings                         doc3                               
```

ex)

* hello를 찾는다?
  * forward index 구조
    * document를 순회하며 hello라는 키워드가 있는지 확인함
  * inverted index 구조
    * word에서 hello를 찾고, 해당 document를 반환

https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html#generic-data-node\
https://opster.com/guides/elasticsearch/glossary/elasticsearch-document/\
<https://www.elastic.co/guide/en/elasticsearch/reference/current/documents-indices.html>\
<https://www.knowi.com/blog/what-is-elastic-search/>
