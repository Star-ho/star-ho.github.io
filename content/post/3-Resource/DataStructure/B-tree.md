---
date: 2024-05-08 22:34:01
updatedAt: 2024-05-08 23:25:54
tags:
  - hugo_blog
  - B-tree
categories:
  - DataStructure
title: B-tree
lastmod: 2024-05-08T14:25:54.070Z
---
* 다수의 엘리먼트가 있을때, 지역성을 가질 수 있는 자료구조
  * 한번읽고, 다음걸 읽을때 빠르게 찾아갈 수 있기 때문

## 특징

* 모든 데이터는 leaf노드에 있음
* non-leaf노드에는 키만 존재
* root노드에서 모든 leaf노드까지는 같음
* 만약, 노드에서 n개의 자식을 가지고 있으면, 이  노드는 n-1개의 키를 가지고 있음
* 루트노드를 제외한 모든 노드는 절반이상 차있음
* 하위노드의 모든 값은, 해당 부모 노드의 양쪽 포인터 값 사이에 있는 키를 가짐
* 루트노드가 leaf노드가 아닌경우, 적어도 2개의 자식을 가지고 있음
* 리프노드의 탐색시간은 O(logN)임

## 예제

* 아래 예제는 branch factor가 5인 B-tree임
  * branching factor가 5이기에, 리프가 아닌 노드들은 5개의 addr값을 가지고 있음\
    ![center|700](/image/real-resource-image/Pasted%20image%2020240508231341.png)

### key29에 있는 값을 찾는 순서

1. 먼저 루트노드에서 10과 50사이의 주소를 찾음
2. 1에서 얻은 주소를 따라가, 25와 37사이의 주소를 얻음
3. 2에서 얻은 주소를 따라가 28과 31사이의 주소를 얻고, 해당 주소에서 값을 찾음

## 삽입과정(tree가 자라나는 과정)

* key 16에 값을 넣는 과정임

![center](/image/real-resource-image/Pasted%20image%2020240508232003.png)

* key 16을 넣으려니, 리프노드에는 5개의 값이 꽉 차있어 분리가 필요함

![center](/image/real-resource-image/Pasted%20image%2020240508232205.png)

* 부모의 노드가 여유가 있어, 부모트리를 나누어 1개의 leaf노드를 2개의 leaf노드로 분리함
* 이후 key 16에 데이터를 추가함

https://www.cs.cornell.edu/courses/cs3110/2012sp/recitations/rec25-B-trees/rec25.html\
https://www.linkedin.com/pulse/data-structures-powering-our-database-part-3-b-trees-saurav-prateek/
