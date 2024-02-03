# QueryDSL

**SQL, JPQL 등을 코드로 작성할 수 있도록 해주는 ✨빌더 오픈소스 프레임워크✨**

Query DSL은 select, from , where 등 쿼리 작성에 필요한 키워드를 **메서드 형식으로 제공**

## QueryDSL JPA

① SQL, **JPQL** 을 코드로 작성할 수 있도록 해주는 빌더 API

② Entity 클래스와 매핑되는 **QClass** 라는 객체를 사용해서 쿼리를 실행

## QClass 란 ? (- @Entity 사용)

QueryDSL 은 컴파일 단계에서 엔티티를 기반으로 QClass 를 생성하는데 JPAAnnotationProcessor 가 컴파일 시점에 작동해서 @Entity 등등의 어노테이션을 찾아 해당 파일들을 분석해서 QClass 를 만든다.

QClass 는 Entity 와 형태가 똑같은 Static Class 이다.

QueryDSL 은 쿼리를 작성할 때 QClass 를 기반으로 쿼리를 실행한다.

## JPQL 이란 ?

JPA 에서 지원하는 다양한 쿼리 방법 중 가장 단순한 조회 방법으로, SQL 의 경우에는 DB 테이블을 대상으로 쿼리를 질의하지만, JPQL 은 엔티티 객체를 대상으로 쿼리를 질의한다.

EX)

```java
String jpql = “select m From Member m where m.name like ‘%hello%’”;
List<Member> result = em.createQuery(jpql, Member.class).getResultList();
```

(SQL 로 변환시) select * from members where name like ‘%hello%’;

### JPQL 의 문제점

① 쿼리를 여전히 문자열로 입력한다.

- > 오타가 발생하거나 관리하는데 있어서 어려움이 따르고, type-check 가 불가능하다

② 컴파일 단계에서 오류를 확인할 수 없고, 런타임에서 해당 쿼리가 실행되어야 오류를 발견할 수 있다.

- > 테스트 코드를 짜면 불안을 덜 수 있긴 하지만, 실제 프로그램을 운영하면서 오류가 발생할 수도 있다는 부담이 너무 커진다.

## QueryDSL JPA  특징

① 쿼리를 여전히 문자열로 입력하기 때문에 오타가 발생하거나 관리하기 어렵다.

➡️ QueryDSL 은 쿼리를 **문자열로서가 아니라 코드를 통해서 작성**하기 때문에 오타가 날 확률이 적어지고, 객체 지향적으로 개발할 수 있다.

② 컴파일 단계에서 오류를 확인할 수 없고, 런타임 시 해당 쿼리가 실행되어야지만 오류를 확인할 수 있다.

➡️ QueryDSL 은 코드로서 작성하기 때문에 컴파일 단계에서도 오류를 빠르게 발견할 수 있다.

예를 들어서

회원(member) 와 포인트(point) 를 조인해서 가져와야 할 때 다음 차이가 있기 때문에, QueryDSL을 사용하면, 오타가 나더라도 컴파일 단계에서 오류를 확인 할 수 있고, 코드로서 작성하기 때문에 더욱 객체 지향적으로 개발할 수 있다.

```java
String jpql = "select * from Member m join Point p on p.member_id = m.id"
List<Member> result = em.createQuery(jpql, Member.class).getResultList();
```

```java
return jpaQueryFactory
.from(member)
.join(member.point, point)
.fetch();
```
