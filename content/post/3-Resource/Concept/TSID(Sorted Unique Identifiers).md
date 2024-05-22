---
date: 2023-10-03T10:13:23
updatedAt: 2024-04-21 18:34:36+2740
tags:
  - Concept
  - hugo_blog
title: TSID(Sorted Unique Identifiers)
lastmod: 2024-04-21T09:36:38.058Z
---
최근에 id 생성 관려해서 정보를 찾아보던중 TSID라는 것을 알게되었다<br><br>twitter에서 개발한 snowflake id generator가 있다  <br>분산처리환경에서 id를 생성하기 위해 만든 id 생성기인데 time값을 기반으로한다<br><br>time값을 기반으로해서 시간순 정렬도 되고, +순차적인 정보라서 값이 겹칠일이 매우 적다.<br><br>time값을 기반으로한 64비트 id generator인데  <br>처음 1비트는 0  <br>다음 41비트는 타임 스탬프값,  <br>다음 10비트는 machine과 관련된 정보(node id 같은것들?)  <br>다음 12비트는 같은 milisecond에 받은 순차적인정보로 이루어진다.<br><br>java에서 snowflake를 구현한 라이브러리가 없는지 찾아봤는데 있긴한데 스타수가 적기도하고 마지막 업데이트가 4년전이라 쓰기도 약간 부담스러워 검색을 해보니 TSID아이디 generator라는걸 찾았다<br><br>TSID는 snowflake\_id와 ulid에서 아이디어를 얻어 만들었다고 한다.<br><br>snowflake와 마찬가지로 처음 42비트는 시간 기반 값, 이후 22비트는 랜덤값(node id+counter)라고 한다.<br><br>개인적으로 UUID보다 TSID가 퍼포먼스가 더 낫고, 다양한 상황에서 더 좋을거라 생각된다  <br>단, star 수가 적고 다양한 환경에서 테스트가 안된점이 아쉽지만 시간이 해결해 줄 문제라고 생각한다<br><br>UUID를 잘 사용하는법  <br><https://www.percona.com/blog/uuids-are-popular-but-bad-for-performance-lets-discuss/>  <br><https://www.percona.com/blog/store-uuid-optimized-way/>  <br><https://vladmihalcea.com/uuid-database-primary-key/><br><br><https://discord.com/developers/docs/reference#snowflakes>  <br><https://github.com/callicoder/java-snowflake>  <br><https://github.com/f4b6a3/tsid-creator>  <br><https://en.wikipedia.org/wiki/Snowflake_ID>|

\#Concept
