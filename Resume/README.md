---
sort: 1
---

# Resume
<div align="right">
 <a href="http://github.io/Star-ho" target="_blank"><img src="http://img.shields.io/badge/-Github-black?style=flat&logo=github"/></a>
 <a href="https://www.linkedin.com/in/starho/0" target="_blank"><img src="https://img.shields.io/badge/LinkedIn-1DA1F2?logo=LinkedIn&logoColor=white&style=flat"/></a>
  <img src="https://img.shields.io/badge/Gmail-FFFFFF?style=style=flat&logo=gmail&logoColor=red&label=starho1994@gmail.com"/>
</div>

# __황성호__
## __Experience__
---
#### 더파이러츠(2021-10 ~) 백엔드 개발(물류 서비스 담당)

#### 시큐어루트(2019-10~2021-04) 취약점 분석 및 모의해킹

## __프로젝트__
### 회사 프로젝트 

### 주문 발주서 엑셀 개선(2022-04)
  - 기존의 주문 발주서의 구조 문제로 발주서를 다운받고 수정하는 일이 빈번하여 주문 발주서 엑셀을 개선함
  - 엑셀에 해당 점포의 추가 정보를 확인할 수 있는 QR코드 삽입
사용기술 : Kotlin, Spring, JPA, poi

### 재고관리기능 추가(2022-03~2022-04)
  - 물류서비스 재고관리 기능 추가
  - 재고, 창고 엔티티 및 주문과 연결하여 재고 감소 로직 개발
  - 재고관리 api개발 및 DB 모델링
사용기술 : Kotlin, Spring, JPA

### 정보 관리 서버 데이터 저장소 변경(2022-02)
 - 내부적 자료에 쓰기위한 데이터 저장소를 MySQL에서 aws 엘라스틱 서치로 변경
 - 기존의 프로젝트에서는 로그관리를 위해 엘라스틱서치를 사용고 있었음
 - 로그를 쌓는 용으로 사용하고 있었고 데이터 저장만이 필요했기에 RestHighLevelClient를 이용하여 구현되어 있었음
 - 하지만 데이터 관리용으로 사용하게 된다면 추후에 조회로직이 추가될 것이라 생각하였고 Spring Data를 사용하는게 더 나을것이라 생각이 들어 Spring Data를 사용함
 - 현재 aws elastic search와 Spring데이터 간의 인증문제를 해결해 주는 라이브러리가 존재하지 않아 추가으로 구현하였음

사용기술 : Spring data, aws elastic search, Kotlin

### 상품예약기능 추가(2022-01~2022-02)
 - 상품 상태와 가격정보를 예약하여 변경할 수 있도록 기능 추가
 - 테이블 및 스케줄러 추가
 - 해당 비즈니스 로직 구현
 - DiscriminatorColumn을 이용하여 엔티티간 상속관계를 구현

사용기술 : Kotlin, Spring, JPA

### 요구사항 변경에 따른 도메인 모델 변경(2021-11~2021-12)
 - 가격과 관련된 정보를 상품에서 판매단위로 이동
 - 이에따라 상품, 판매단위, 할인정책, 주문 엔티티가 변경에 따른 수정
 - 엔티티 변경에 따른 request, response값 수정

사용기술 : Kotlin, Spring, JPA

### 단가관리 기능 구현(2021-01)
 - 상품의 단가를 한 페이지에서 관리할 수 있도록 구현
 - 단가 수정사항의 정보 히스토리를 저장하도록 테이블 추가 및 구현 
 - 상품수정페이지에서 가격 정보 변경시 가격 정보 히스토리를 쌓도록 로직 추가

사용기술 : Kotlin, Spring, JPA

### 수산시장 서비스 버그 수정(2021-12)
 - 수산시장 서비스 댓글, 주문목록, 메시지관리 버그 수정
 - 비밀글의 댓글이 비밀글이 되지 않는 문제 해결
   - 백엔드에서 처리하지 않고 프론트에서 부모 댓글이 비밀글일때 자식댓글도 비공개 처리하고 있었음
   - 개발자 도구를 사용하면 데이터 확인이 가능하기에 서버 로직을 변경함
   - 추가로 데이터베이스 프로시저를 이용하여 부모댓글이 비밀글인 댓글을 비밀글 처리함
 - 주문목록의 페이징 처리와 정렬이 되지 않는 문제 해결
   - 주문목록을 가져오는 SQL문이 추가되는 요구사항으로 인해 매우 복잡하였음
   - 해당 로직을 보고 SQL문을 새로 작성함
 - 판매자에게 가야할 메시지가 소비자에게 가는 문제 해결
   - 판매자를 가져와야하는 로직이 소비자를 가져오도록 구현되어 있어서 변경함

사용기술 : JAVA, Spring, Mybatis

### 고객등급 기능 구현(2021-11~2021-12)
 - 고객들의 등급을 설정하고 이에 대해 가격을 차별화 하는 기능 구현
 - 주문시 해당 고객이 해당 상품에 대해 할인이 존재하는지 확인 후 할인 가격이 존재한다면 할인하도록 구현함
 - 할인정보가 상품이 아닌, 카테고리, 어종, 상품, 판매단위 별로 설정할 수 있었기에 할인정보를 그래프로 만들어서 확인하도록 구현함
 - 테이블 추가 및 기존 테이블 수정
 - 클라이언트 페이지에서 가격을 볼 수 있도록 가격정보 추가

사용기술 : Kotlin, Spring, JPA

### 멀티프로젝트로 전환(2021-10~2021-11)
 - 기존 물류 서버는 백오피스와 클라이언트에 대한 요청을 하나의 서버에서 처리함
 - 보안상의 문제로 백오피스와 클라이언트 요청에 대한 서버를 분리하기로 결정함
 - 초기에는 프로젝트를 분리하는 것이 목표였음
 - 프로젝트를 분리시 중복되는 코드가 많기 때문에 버그 발생 가능성이 높아 멀티 프로젝트로 전환
 - 도메인에 대한 정확한 이해 부족으로 우선 common모듈과 백오피스모듈, 클라이언트모듈 3개로 나눔
 - common모듈에는 controller를 제외한 도메인, 서비스 등이 들어있고, 백오피스와 클라이언트 모듈에는 각각의 contoller를 둠
 - 지속적으로 변경할 예정
 - 하나의 서버가 두개로 분리되었기에 배포를 위해 Docker파일과 application.yml 파일 수정

사용기술 : Gradle

### 개인 프로젝트 

### 클린아키텍처 어플리케이션 개발(2022-04~)
 - 클린아키텍처 책을 읽고 클린아키텍처 어플리케이션 개발을 시작함
 - 간단한 주문도메인 예시로 현재 업무에 적용하려면 어느정도 까지 의존성을 줄일지 고민
 - 이외에 이때까지 공부해봤던 것들을 적용해볼 계획
사용기술 : kotlin, Spring, JPA, Clean Architecture

### 백엔드 코드 리팩토링(2022-01~2022-04)
 - 기존 코드를 작성할 때 잘 모를때여서 코드를 기능중심으로 작성하였음
 - 재사용 가능한 코드는 재사용 할 수 있게 변경함
 - 코드를 깔끔하게 변경함

사용기술 : JAVA, Spring

https://github.com/Star-ho/modusaleServer

### 위치정보를 기반하여 할인정보를 찾도록 변경(2022-01)
 - 위치정보에 따라 할인정보가 다른 앱을 확인함
 - 현재 위치정보를 확인하여 할인정보를 알려주도록 변경함
 - 백엔드
   - 위치정보에 따라 할인정보가 다른 API를 확인함
   - 해당 프론트에서 위치정보를 보내면 해당 정보를 API로 보냄
 - 프론트엔드
   - expo-location라이브러리를 사용하여 위치정보를 확인함

사용기술: JAVA, Spring, react-native

백엔드 코드
 - https://github.com/Star-ho/modusaleServer

프론트엔드 코드
 - https://github.com/Star-ho/modusaleFront

### 할인모아 서비스를 JAVA/Spring으로 변경함(2021-09)
 - node로는 취업의 어려움을 느껴 대중적인 JAVA/Spring으로 프로젝트를 변경함

사용기술 : JAVA, Spring

https://github.com/Star-ho/modusaleServer

### 할인모아 서비스 nodeJS로 구성(2021-06~2021-09)
 - 백엔드
   - 할인모아 서비스는 데이터 요청이 많이 일어나므로 초창기에는 비동기 처리가 쉬운 nodeJS(express JS)로 구현함
   - 배달앱들의 로직을 분석해서 할인정보를 가져오는 API를 확인함
   - 배달앱별로 리턴이 JSON, HTML, image(현재 변경됨)가 있었음
     - JSON데이터들은 객체로 HTML은 정규표현식으로 파싱하였음
     - Image데이터들은 OCR라이브러리(tesseract)를 사용하여 파싱함
       - telegram API를 사용하여 할인 가격이 일정 가격대 이상일시 핸드폰으로 알람이 오도록 구현
   - telegram Bot을 이용하여 텔레그램을 이용하여 할인정보를 조회, 추가, 변경, 삭제할 수 있도록 구현함
   - 일정시간마다 데이터를 가져오도록 스케줄러 구현
   - 받아온 데이터를 저장하기 위해 Mysql 사용
   - aws EC2에 서버를 올림
   - AWS Certicate를 이용하여 HTTPS 적용
   - Route 53을 사용하여 도메인 연결함
 - 프론트엔드
  - react-native, expo를 이용하여 앱을 구현함
  - google Admob을 이용하여 광고 추가함

사용기술 : nodeJS, expressJS, tesseract, expo, react-native, admob

백엔드 코드
 - https://github.com/Star-ho/modosale
 
프론트엔드 코드
 - https://github.com/Star-ho/modusaleFront

## __Skills__

Programming : Kotlin, Java, nodeJS

FrameWork : Spring

DataBase : Mysql, H2


## __Certification__

정보보안기사(2019-06)

정보처리기사(2018-04)

