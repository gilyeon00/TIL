# JobRepository

- 배치 작업 중의 정보를 저장하는 저장소 역할
- Job이 언제 수행되었고, 언제 끝났으며, 몇번 실행되었고, 실행에 대한 결과 등의 배치 작업의 수행과 관련된 모든 meta data 를 저장
    - JobLaunvher, Job, Step 구현체 내부에서 CRUD 기능을 처리

      <img width="961" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/ec9b9aaf-2a4c-42d8-8bcc-4b8fd93a9c5f">

      <img width="719" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/45819d1e-ad2f-4530-8317-0d6287b0fd6e">


## JobRepository 설정

우리는 지금까지 JobRepository를 따로 설정한 기억이 없다. 그러면 언제, 어떻게 JobRepository 를 설정하고, 생성됐을까?

- `@EnableBatchProcessing` 어노테이션만 main 클래스에 선언하면, JobRepository 가 자동으로 빈으로 생성된다.
- BatchConfigurer 인터페이스를 구현하거나, BasicBatchConfigurer 를 상속해서 JobRepository 설정을 커스터마이징할 수 있다.
    - 커스터마이징이 필요할 때는 언제일까?
    - **JDBC 방식으로 설정 - JobRepostitoryFactoryBean**
        - 내부적으로 AOP 기술을 통해 트랜잭션 처리를 해주고 있다.
        - 트랜잭션 isolation 의 기본값은 SERIALIZEBLE 로 최고 수준, 다른 레벨(READ_COMMITED, REPEATABLE_READ) 로 지정 가능하다.
        - 메타 테이블의 Table Prefix 를 변경할 수 있음. 기본 값은 “BATCH_”임
    - **In Memory 방식으로 설정 - MapJobRepositoryFactoryBean**
        - 성능 등의 이유로 도메인 오브젝트를 굳이 데이터베이스에 저장하소 싶지 않을 경우에 사용
        - 보통 Test 나 프로토타입의 빠른 개발이 필요할 때 사용