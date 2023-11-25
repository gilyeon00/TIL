# JpaCursorItemReader

- Spring Batch 4.3 버전부터 지원
    - 해당 버전이전에는 JDBC Cursor 방식을 지원했음
- Cursor 기반의 JPA 구현체로서, EntityManagerFactory 객체가 필요하며, 쿼리는 JPQL 을 사용한다.
- Step 은 JpaCursorItemReader 을 통해서 데이터를 읽을 수 있다.

<img width="715" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/793d4ad9-de7d-4794-a3fd-55955fa8df82">

### ✈️ JpaCursorItemReader 가 데이터를 읽기 전에, ItemStream 에서 하는 일 !

1. Step 은 ItemStream 의 open() 을 통해 EntityManager 를 만든다
2. EntityManager 를 생성하면, 우리가 설정한 JPQL 을 통해서 query 문을 생성하게 된다
3. JPQL 로 생성된 최종 값이 ResultList 로 반환된다
4. List 로 반환된 값은 Stream 으로 바꾼다 (ResultStream)
5. 이 ResultStream 을 통해 Iterator 로 만들어낸다.