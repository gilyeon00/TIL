# SimpleJob

- SimpleJob 은 Step 을 실행시키는 Job의 구현체로서, SimpleJobBuilder 에 의해 생성된다
- 여러 단계의 Step 으로 구성할 수 있으며, Step 을 순차적으로 실행시킨다.

    ```java
    @Bean
        public Job job () {
            return jobBuilderFactory.get("my-job")
                    .start(step1())
                    .next(step2())
                    .build();
        }
    ```

- 모든 Step 의 실행이 성공적으로 완료되어야 Job 이 성공적으로 완료된다.
    - 밑의 내용 확인해보기
    - JobBuilderFactory (JobBuilder 생성)
      → JobBuilder (SimpleJobBuilder 생성)
      → JobLauncher (Job 실행)
      → SimpleJob (JobInstance 생성)
      → JobInstance (JobExecution 생성)
      → Step (StepExecution 생성)
      → StepExecution
      → 성공? 실패?
      ➡️ 마지막 StepExecution 의 성공 여부는 곧 Job의 성공 여부
- 맨 마지막에 실행한 Step 의 BatchStatus 가 Job 의 최종 BatchStatus 가 된다.

  ![스크린샷 2023-09-17 오후 11.42.16.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/ac62c3f4-f23c-4ffc-9896-937ba59d95d3/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-17_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.42.16.png)


```java
@Bean
    public Job job () {
        return jobBuilderFactory.get("my-job")
                .start(step1())
                .next(step2())
								.incrementer(new RunIdIncrementer())
                .build();
    }
```

- **jobBuilderFactory.get("my-job")**
    - JobBuilderFactory 로 부터, JobBuilder 를 생성
    - 매개변수로 해당 Job 의 이름을 받음
- **start(step1())**
    - start(Step)
    - 처음 실행 할 Step 설정
    - 이 메서드를 실행하면 `SimpleJobBuilder` 가 반환됨
- **next(step2())**
    - next(Step)
    - 다음에 실행 할 Step 설정 (개수는 무제한)
    - 모든 next() 의 Step 이 종료되면, Job 이 종료된다
- **incrementer(new RunIdIncrementer())**
    - incrementer(JobparmetersIncrementer)
    - JobParameter 의 값을 자동으로 증가해주는 JobparmetersIncrementer 설정
- **preventRestart(true)**
    - Job 의 재시작 가능 여부 설정 (기본값 true)
- **validator(JobParameterValidator)**
    - JobParameter 를 실행하기전, 올바른 구성인지 검증하는 JobParameterValidator 설정
    - JobParameterValidator
        - 우리는 Job 을 실행할 때, <Key, Value> 로 된 JobParameter 를 저장하고 실행함 → 그 값이 제대로 저장이 되었는 지 등을 확인 (필수 값 여부 확인 등)
- **listener(JobExecutionListener)**
    - Job 이 실행이 되면, Job의 실행 전 or 후에 Listener 를 사용해서 콜백을 제공받을 수 있도록 JobExecutionListener 설정
- **build();**
    - SimpleJob 생성