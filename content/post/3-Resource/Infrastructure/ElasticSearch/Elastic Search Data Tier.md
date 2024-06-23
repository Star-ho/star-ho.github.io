---
date: 2023-10-04T22:54:59
updatedAt: 2024-06-23 20:37:25
tags:
  - ElasticSearch
  - hugo_blog
categories:
  - ElasticSearch
title: Elastic Search Data Tier
lastmod: 2024-06-23T11:39:19.677Z
---
* Data Tier는 Content, Hot, Warm, Cold, Frozen 5개의 티어로 나눔

* Data Tier는 정말 사용하는 데이터(상품의 카탈로그)같은 것으로 다른 티어들과 다르게 시간의 지남에 따라 티어를 이동하지 않음

* Hot, Warm, Cold, Frozen는 Time series data의 관리시 매우 유용함
  * 특히 시계열 인덱스일때 매우 유용

* 많은 경우, 자주 접하는 데이터와 자주 접하지 않는 데이터가 나누어 
  * 시계열일 경우 최근 데이터를 자주보고 시간이 지난 데이터를 덜 볼 것임
  * ex) 로그 데이터

* Hot으로 갈수록 더 최근, 자주 접근하는 데이터이고 Frozen으로 갈수로 옛날, 잘 접근하지 않는 데이터

* 데이터 관리 방법
  * 자주 접근하는 티어(ex Hot tier)는 더 좋은 장비(SSD, memory)를 두고 덜 접근하는 티어(Fozen)에는 낮은장비, 혹은 스냅샷을 이용하여 저장하고 필요시 복구하는 정도

* 티어를 나눔으로써 장비에 대한 비용을 아낄 수 있음

> 하나의 샤드의 크기를 50GB이하로 유지할것, elastic search는 200M이상 50GB이하일때 성능이 가장 좋음

https://www.elastic.co/guide/en/elasticsearch/reference/current/data-tiers.html\
https://www.elastic.co/kr/blog/implementing-hot-warm-cold-in-elasticsearch-with-index-lifecycle-management
