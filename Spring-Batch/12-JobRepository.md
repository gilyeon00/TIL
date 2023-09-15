# JobRepository

- 배치 작업 중의 정보를 저장하는 저장소 역할
- Job이 언제 수행되었고, 언제 끝났으며, 몇번 실행되었고, 실행에 대한 결과 등의 배치 작업의 수행과 관련된 모든 meta data 를 저장
    - JobLaunvher, Job, Step 구현체 내부에서 CRUD 기능을 처리

      <img width="961" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/ccadbdff-f6a3-40a0-bfc8-fdcef194c123">

      <img width="719" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/340aff6a-4f90-4f5a-9dc7-c6ce2bf73aa4">


## JobRepository 설정

우리는 지금까지 JobRepository를 따로 설정한 기억이 없다. 그러면 언제, 어떻게 JobRepository 를 설정하고, 생성됐을까?

- `@EnableBatchProcessing` 어노테이션만 main 클래스에 선언하면, JobRepository 가 자동으로 빈으로 생성된다.
- BatchConfigurer 인터페이스를 구현하거나, BasicBatchConfigurer 를 상속해서 JobRepository 설정을 커스터마이징할 수 있다.
    - **JDBC 방식으로 설정 - JobRepostitoryFactoryBean**
        - JobRepository는 JobRepositoryFactoryBean이라는 빈을 통해 생성된다.
        - 내부적으로 AOP 기술을 통해 트랜잭션 처리를 해주고 있다.
            - ➡️ 별도의 트랜잭션 처리를 해주지 않아도 된다
        - 트랜잭션 isolation 의 기본값은 SERIALIZEBLE 로 최고 수준, 다른 레벨(READ_COMMITED, REPEATABLE_READ) 로 지정 가능하다.
            - ➡️ SERIALIZEBLE : 가장 엄격한 수준
                - 일반적으로 웹어플리케이션 같은 경우엔, 각각의 사용자가 DB에 접근할 때마다 엄격하게 처리되기 때문에 속도/성능 저하 발생 가능
                - But, 배치 같은 경우 동시에 많은 사용자가 접근하지 않기 때문에 (트래픽이 쏠리지 않기때문) 엄격하게 설정해도 문제 없다
        - 메타 테이블의 Table Prefix 를 변경할 수 있음. 기본 값은 “BATCH_”임
    - **In Memory 방식으로 설정 - MapJobRepositoryFactoryBean**
        - 성능 등의 이유로 도메인 오브젝트를 굳이 데이터베이스에 저장하소 싶지 않을 경우에 사용
        - 보통 Test 나 프로토타입의 빠른 개발이 필요할 때 사용



### JobRepository 인터페이스의 구현체 : **SimpleJobRepository**

<img width="612" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/ee7579c6-59c6-4e1f-a066-86eaadccbaa4">

- 우리가 지금까지 봐왔던 JobInstance, JobExecution, StepExecution, ExecutionContext 객체가 만들어지는 것을 볼 수 있다. (객체를 만들어서 JobRepository를 통해 DB에 저장해야하므로)

### JobInstanceDao 인터페이스의 구현체 : **JdbcInstanceDao**

<img width="742" alt="4" src="https://github.com/gilyeon00/TIL/assets/52391627/1bec6e8c-a24b-4ef2-9d8d-36abb9e581f0">

- 해당 객체들을 DB에 저장할 때 사용하는 것을 알 수 있음

<img width="795" alt="5" src="https://github.com/gilyeon00/TIL/assets/52391627/098df08d-9a1f-442b-9e5e-a394d8a62a96">

- JobInstance 가 없다면 createJobInstance 를 하고, 있다면 전에 실행됐던 JobExecution을 가져와서 상태를 확인하는 것을 볼 수 있음

<img width="882" alt="6" src="https://github.com/gilyeon00/TIL/assets/52391627/6a39bdd3-3232-4ff1-8b92-c5d84aa77f5c">

- Step 은 재시작 / skip 선택지가 있다. isRestart 통해 skip할지, StepExecution을 생성해서 restart 할 지 분기한다.