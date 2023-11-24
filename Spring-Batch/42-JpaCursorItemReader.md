# JpaCursorItemReader

- Spring Batch 4.3 버전부터 지원
    - 해당 버전이전에는 JDBC Cursor 방식을 지원했음
- Cursor 기반의 JPA 구현체로서, EntityManagerFactory 객체가 필요하며, 쿼리는 JPQL 을 사용한다.
- Step 은 JpaCursorItemReader 을 통해서 데이터를 읽을 수 있다.

<img width="715" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/793d4ad9-de7d-4794-a3fd-55955fa8df82">