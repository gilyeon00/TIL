# Spring Batch Test

- Spring Batch 4.1.x 이상 버전 (Boot 2.1) 기준

## application.yml (Gradle)

```json
// https://mvnrepository.com/artifact/org.springframework.batch/spring-batch-test
testImplementation 'org.springframework.batch:spring-batch-test:4.2.2.RELEASE'
```

## @SpringBatchTest

: 자동으로 ApplicationContext 에, 테스트에 필요한 여러 Util Bean 을 등록해주는 어노테이션

- **JobLauncherTestUtil**
    - launchJob(), launchStep() 과 같은 스프링 배치 테스트에 필요한 유틸성 메소드 지원
        - launchJob(), launchStep() 각각 Job, Step 을 실행시키고 JobExecution 을 반환
- **JobRepositoryTestUtil**
    - JobRepository 를 사용해서 JobExecution 생성 및 삭제 기능 메서드 지원
- **StepScopeTestExecutionListener**
    - @StepScope 컨텍스트를 생성해주며, 해당 컨텍스트를 통해 JobParameter 등을 단위 테스트에서 DI 받을 수 있다.
- **JobScopeTestExecutionListener**
    - @JobScope 컨텍스트를 생성해주며, 해당 컨텍스트를 통해 JobParameter 등을 단위 테스트에서 DI 받을 수 있다.