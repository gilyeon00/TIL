# JPA를 왜 쓸까
- SQL 중심적인 개발이 아닌, 객체 중심의 개발 ⇒ 생산성, 유지보수성 증대
- 슈퍼/서브타입에서 서브타입에 넣으면, JPA에서 자동으로 슈퍼타입에도 넣어줌
- 자유로운 객체 그래프 탐색  ⇒ 엔티티 객체 신뢰성 보장
    - 그렇다고 엄청난 SQL문을 날리진 않음 (지연 로딩 덕)
- 동일한 트랜잭션에서 조회한 엔티티는 같음을 보장
- 성능 최적화 기능
    1. 1차 캐시와 동일성 보장
        1. 같은 트랜잭션 안에서는 같은 엔티티 반환 - 약간의 조회 성능 향상
            ![1](https://github.com/gilyeon00/TIL/assets/52391627/0cf387f6-dc05-4b3a-895a-eee2aa16ca36)

    2. 트랜잭션을 지원하는 쓰기 지연 (Insert)
        1. 트랜잭션을 커밋할 때까지 Insert SQL문을 모음
        2. JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송=
           ![2](https://github.com/gilyeon00/TIL/assets/52391627/e008c0ef-5272-4375-b38f-6ccd741fed36)

    3. 트랜잭션을 지원하는 쓰기 지연 (Update)
        1. Update, Delete로 인한 row락 시간 최소화
        2. 트랜잭션 커밋 시  Update, Delete SQL 실행하고, 바로 커밋
           ![3](https://github.com/gilyeon00/TIL/assets/52391627/2dba50da-3147-4b7a-bead-634ad155cafb)

    4. 지연 로딩과 즉시 로딩
        - 지연 로딩 : 객체가 실제 사용될 때 로딩
        - 즉시 로딩 : Join SQL 로 한 번에 연관된 객체까지 미리 조회
        ![4](https://github.com/gilyeon00/TIL/assets/52391627/c9a01eed-cd56-4bc2-8b16-bdf8a6df59fb)


- Java Persistance API
    - 자바 표준 ORM
    - ORM 프레임 워크가 중간에서 매핑  (객체랑 RDB랑 사이)까


---
## Ref
- [인프런] 자바 ORM 표준 JPA 프로그래밍 - 기본편 : 김영한