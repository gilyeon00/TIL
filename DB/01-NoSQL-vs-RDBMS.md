# NOSQL vs RDBMS

- **Databse**란 일반적으로 컴퓨터 시스템에 전자 방식으로 저장된 구조화된 정보 또는 데이터의 체계적인 집합을 의미
- **DBMS**란(DataBase Management System) 사용자와 데이터베이스 사이에서 사용자의 요구에 따라 정보를 생성해 주고 데이터베이스를 관리해 주는 소프트웨어
- **SQL**이란(Strucured Query Language) 관계형 데이터베이스 관리 시스템의 데이터를 관리하기 위해 설계된 특수 목적의 프로그래밍 언어이다.
  관계형 데이터베이스 관리 시스템에서 자료의 검색과 관리, 데이터베이스 스키마 생성과 수정, 데이터베이스 객체 접근 조정 관리를 위해 고안됨

## RDBMS

이름과 같이 RDBMS는 RDB를 관리하는 시스템이며 RDB는 관계형 데이터 모델을 기초로 두고 모든 데이터를 2차원 테이블 형태로 표현하는 데이터베이스

- 관계형 데이터베이스(RDMBS)에서는 이러한 관계를 나타내기 위해 외래 키(foreign key) 사용
- 이러한 테이블간의 관계에서 외래 키를 이용한 테이블 간 Join이 가능하다는 게 RDBMS의 가장 큰 특징

## NoSQL

NoSQL에서는 RDBMS와는 달리 테이블 간 관계를 정의하지 않습니다. 데이터 테이블은 그냥 하나의 테이블이며 테이블 간의 관계를 정의하지 않아 일반적으로 테이블 간 Join도 불가능

NoSQL은 점점 빅데이터의 등장으로 인해 데이터와 트래픽이 기하급수적으로 증가함에 따라 **RDBMS에 단점인 성능을 향상시키기 위해**서는 장비가 좋아야 하는 Scale-Up의 특징이 비용을 기하급수적으로 증가시키기 때문에 데이터 일관성은 포기하되 비용을 고려하여 **여러 대의 데이터에 분산하여 저장하는 Scale-Out을 목표**로 등장

## 📌 Key-Value Database

- Key-Value Database는 데이터가 Key와 Value의 쌍으로 저장된다.
- Key는 Value에 접근하기 위한 용도로 사용된다. → Key 값은 unique한 고유값으로 유지되어야 한다.
- 값은 어떠한 형태의 데이터라도 담을 수 있다. ~~심지어는 이미지나 비디오도 가능하다.~~ 또한 간단한 API를 제공하는 만큼 질의의 속도가 굉장히 빠른 편이다.
- 대표적인 NoSQL Key-Value Model로는 `Redis`, Riak, Amazon Dynamo DB 등이 있다.
- 주로 다음 경우에 사용

    1. 성능 향상을 위해 관계형 데이터베이스에서 데이터 캐싱

    2. 장바구니 같은 웹 애플리케이션에서 일시적인 속성 추적

    3. 모바일 애플리케이션용 사용자 데이터 정보와 구성 정보 저장

    4. 이미지나 오디오 파일 같은 대용량 객체 저장 `<< 다시 알아보기`

## 📌 Document Database

- Documnet Database 데이터는 Key와Document의 형태로 저장된다.
- **Document Database는 값을 문서로 저장한다** (**보통 JSON이나 XML 같은 표준 형식)**
- 객체지향에서의 객체와 유사하며, 이들은 하나의 단위로 취급되어 저장된다. 다시 말해 하나의 객체를 여러 개의 테이블에 나눠 저장할 필요가 없어진다는 뜻이다.
- 주요한 특징으로는 객체-관계 매핑이 필요하지 않다. 객체를 Document의 형태로 바로 저장 가능하기 때문이다. 또한 검색에 최적화되어 있는데, 이는 Ket-Value 모델의 특징과 동일하다. 단점이라면 사용이 번거롭고 쿼리가 SQL과는 다르다는 점이다. 도큐먼트 모델에서는 질의의 결과가 JSON이나 xml 형태로 출력되기 때문에 그 사용 방법이 RDBMS에서의 질의 결과를 사용하는 방법과 다르다.
- 대표적인 NoSQL Document Model로는 `MongoDB`, CouthDB 등이 있다.
- 주로 다음 경우에 사용

    1. 대용량 데이터를 읽고 쓰는 웹 사이트 용 백엔드 지원

    2. 제품처럼 다양한 속성이 있는 데이터 관리

    3. 다양한 유형의 메타데이터 추적

    4. JSON 데이터 구조를 사용하는 애플리케이션

    5. 비정규화된 중첩 구조의 데이터를 사용하는 애플리케이션

## 📌 Wide Column Database (= ****Column Family Database)
`좀더 공부하기`**

<img width="743" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/1a68676b-5fe1-4218-8f4d-e3d1117bbc21">

- 컬럼 패밀리 데이터베이스는 대용량 데이터, 읽기와 쓰기 성능, 고가용성을 위해 설계됨
- Column과 Row와 같이 Relation Database와 동일한 용어를 사용하여 스키마를 정의
- 컬럼 수가 많다면 관련된 컬럼들을 컬렉션으로 묶을 수 있다.
- 예를 들어 이름의 성과 이름을 하나로 묶고, 사무실, 핸드폰 등의 전화번호들을 하나로 묶을 수 있다. 이렇게 묶인 컬럼들을 **Column Family**

  <img width="681" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/18baeb44-b97b-4948-be64-822b52ce0540">

- Column-family Model 기반의 Database이며 이전의 모델들이 Key-Value 값을 이용해 필드를 결정했다면, 특이하게도 이 모델은 **키에서 필드를 결정**한다.
- 키는 Row(키 값)와 Column-family, Column-name을 가진다. 연관된 데이터들은 같은 Column-family 안에 속해 있으며, 각자의 Column-name을 가진다.
- 관계형 모델로 설명하자면 어트리뷰트가 계층적인 구조를 가지고 있는 셈이다. 이렇게 저장된 데이터는 하나의 커다란 테이블로 표현이 가능하며, 질의는 Row, Column-family, Column-name을 통해 수행된다.
- 대표적인 NoSQL Column-family Model로는 `HBase`, `Cassandra`**,** Hypertable 등이 있다.
- 주로 다음 경우에 사용

    1. 데이터베이스에 쓰기 작업이 많은 애플리케이션

    2. 지리적으로 여러 데이터 센터에 분산되어 있는 애플리케이션

    3. 복제본 데이터가 단기적으로 불일치하더라도 큰 문제가 없는 애플리케이션

    4. 동적 필드를 처리하는 애플리케이션

    5. 수백만 테라바이트 정도의 대용량 데이터를 처리할 수 있는 애플리케이션

### **HBase** : Hadoop의 HDFS를 저장소로 사용함.

- Master-Slave 구조 : **Master가 데이터 일관성을 보장**

- Master가 장애 발생시 접근 불가 → HA 구성으로 해결

- 1개 node에 쓰기가 집중되는 현상(hot spot) 발생 ☞ 쓰기성능 ↓

- hotspot을 해결하기 위한 작업때문에 performance가 또 떨어짐

- raw key로 정렬 가능

### **Cassandra** : Ring 구조의 Multi Master 복제 지원

- **모든 노드가 동일한 역할 수행** : 일관성의 문제 발생 가능성

- 확장성은 대단히 뛰어남

- 쓰기성능 ↑

- equal query 성능 매우 좋음.

- raw key로 정렬 불가능.

- Netflix에서는 file을 쪼개서 binary형태로 cassandra에 저장하여 효과적으로 사용.