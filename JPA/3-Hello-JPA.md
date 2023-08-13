# JPA의 특징 - 데이터베이스 방언

- JPA는 특정 데이터베이스에 종속적이지 않음
    - 각각의 데이터베이스가 제공하는 SQL문법과 함수는 조금씩 다름
        - 가변 문자 : `MySQL` - VARCHAR, `Oracle` - VARCHAR2
        - 문자열 자르기  : `MySQL` - SUBSTRING(), `Oracle` - SUBSTR()
        - 페이징 : `MySQL` - LIMIT, `Oracle` - ROWNUM
- EntityManagerFactory 는 하나만 생성해서 애플리케이션 전체에서 공유한다,
- EntityManager 는 쓰레드 간의 공유를 절대 하면 안된다. (사용하고 버려야됨. 마치 DB Connection 처럼)
- JPA 의 모든 데이터 변경은 트랜잭션 안에서 실행되어야함

# JPA 구동 방식

1. Persistence 가 persistence.xml (application.yml) 을 읽고 설정 정보 조회
    1. 어떤 DB를 쓸건지 ( MySQL, H2, Oracle.. )
    2. DB에 대한 사용자 정보 ( user 이름, 비밀번호, 데이터베이스 명 등)
    3. javax.persistence 라이브러리
2. 읽은 설정 정보를 토대로 EntityManagerFactory 생성
3. EntityManagerFactory 에서 필요할 때마다 EntityManager 생성

# 순수 JPA 예제 ( Spring X ) - Create

<img width="1060" alt="44" src="https://github.com/gilyeon00/TIL/assets/52391627/9fd98415-43a2-4b66-8525-53deb77dd886">

- EntityManager 는 내부적으로 DB Connection 을 물고 있다 → 닫아줘야함
- JPA는 create, update, delete 시 무조건 한 transaction 안에 있어야한다 → Transaction 설정 필
- Spring 과 같이 사용한다면, EntityManagerFactory,  EntityManger 열고 닫고 등 번거로운일 안해줘도됨

# Update

<img width="1119" alt="55" src="https://github.com/gilyeon00/TIL/assets/52391627/c67dadb7-60b7-4c6b-ac3f-d4d495c607b3">

- JPA를 통해서 가져온 객체는 마치 Collection(List) 쓰듯이, 따로 저장을 안해줘도 된다.
- JTransaction 을 커밋하는 시점에 변경 여부를 체크해서, 변경됐으면 Update 쿼리를 만들어서 날려줌

# JPQL

- JPA 는 SQL 를 추상화한 JPQL 이라는 객체 지향 쿼리 언어 제공
- JPQL 을 사용하면 엔티티 객체를 중심으로 개발하게 됨 ← **JPQL 은 엔티티 객체를 대상으로 쿼리**

```java
List<Member> result = em.createQuery("select m from Member as m", Member.class).getResultList();
```

- JPQL에서의 “Member”은 테이블이 아닌, “객체” 를 의미한다.
- ⇒ Member 객체 모두 다 가지고 와 ‼️

[ 문제점 ]

- 검색을 할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색하게 됨
- → 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능
- → 애플리케이션이 필요한 데이터만 DB에서 불러오려면, 결국 검색 조건이 포함된 SQL이 필요함
- ⬅ 그렇게 되면 결국 DB에 종속적이게 된다.