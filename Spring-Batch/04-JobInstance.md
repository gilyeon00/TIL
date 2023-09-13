# JobInstance
- Job이 실행될 때 생성되는 Job의 논리적인 실행 단위 객체 로서, 고유하게 식별 가능한 작업 실행을 나타낸다
- Job의 설정과 구성은 동일하지만 Job이 실행되는 시점에 처리하는 내용은 다르기 때문에 Job의 실행을 구분해야됨
    - ex. 하루에 2번씩 배치 Job이 실행된다면 매일 실행되는 각각의 Job을 JobInstance로 표현한다.
- JobInstance 생성 및 실행
    - 처음 시작하는 Job + JobParameter 일 경우 새로운 JobInstance 리턴
    - 이전과 동일한 Job + JobParameter 으로 실행할 경우 이미 존재하는 JobInstance 리턴
        - 내부적으로 JobName + jobKey (jobParametes 의 해시값)를 가지고 JobInstance 객체를 얻음
        - → `BATCH_JOB_INSTANCE` 테이블에서, JOB_NAME, JOB_KEY가 동일한 데이터는 중복해서 저장 불가하다.
- Job 과는 1:M 관계

## Job이 수행될 때 JobInstance가 생성되는 시나리오

<img width="863" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/70dbc4e2-8977-45e0-af1c-f5a6f767db43">

1. `JobLauncher`는 우리가 실행할 배치Job을 실제 실행 시키는 클래스
    1. 실행시키려면 run(job, parameters)를 수행해야함
        1. job 객체, job parameters 객체가 필요
    2. ‘일별 정산’이라는 Job이 있다고 하자.
    3. JobParameters에는 2023.08.21 이라는 데이터값을 가지고 있음

`JobRepository` : Job을 실행 중에 발생하는 메타 데이터들을 DB에 저장하고 업데이트하는 클래스

1. JobRepository은 Job, JobParameters 를 가지고 DB에 확인함
    1. 현재 실행중인 이 Job이 처음 실행되는 앤지, 이 전에 한 번이라도 실행됐던 앤지
    2. 만약 있다면
        1. 기존의 값을 가진 JobInstance를 리턴, Job 실행 되지않음
    3. 만약 없다면
        1. 새로운 JobInstance를 생성

## ApplicationRunner

: Spring boot가 초기화되고 완료되면, 가장 먼저 호출되는 인터페이스

```java
@Component
@RequiredArgsConstructor
public class JobRunner implements ApplicationRunner {

    private final JobLauncher jobLauncher;
    private final Job job;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        JobParameters jobParameters =
                new JobParametersBuilder()
                        .addString("name", "user3")
                        .toJobParameters();

        jobLauncher.run(job, jobParameters);
    }
}
```

### JobLauncher

: Job을 실행시키는 역할

- run(job, parameters) 으로 job을 실행시키는데, 그러려면 Job, JobParameters 2개의 객체가 필요하다.

- JobLaunber : 스프링 배치가 실행될때 이미 빈으로 생성이 되어있음 → 의존성 주입을 받을 수 있음
- Job : 우리가 빈으로 설정해놨으니 의존성 주입 받을 수 있음

<img width="878" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/38bbc411-d9d5-4c82-98d3-5d0e59ddd2c8">

## 1개의 Job으로 여러 개의 JobInstance를 생성하는 방법

1. 제공되는 RunIdIncrementer 사용

```java
@Bean
    public Job job () {
        return jobBuilderFactory.get("simple-job-2")
                .incrementer(new RunIdIncrementer())
                .start(step1())
                .next(step2())
                .build();
    }
```

- Spring Batch 에서 전달받은 Job Parameter 외에 `run.id`라는 **임의의 파라미터를 추가**로 사용해 매번`run.id` 값을 변경해준다.
  매 실행마다 `run.id`  가 변경되니 재실행 가능

1. JobParameters Builder 이용

```java
@Override
    public void run(ApplicationArguments args) throws Exception {
        JobParameters jobParameters =
                new JobParametersBuilder()
                        .addString("name", "user4")
                        .toJobParameters();

        jobLauncher.run(job, jobParameters);
    }
```

1. Custom RunIdIncrementer 사용

```java
public class UniqueRunIdIncrementer extends RunIdIncrementer {
    private static final String RUN_ID = "run.id";

    @Override
    public JobParameters getNext(JobParameters parameters) {
        JobParameters params = (parameters == null) ? new JobParameters() : parameters;
        return new JobParametersBuilder()
                .addLong(RUN_ID, params.getLong(RUN_ID, 10000L) + 100)
                .toJobParameters();
    }
}
```

<img width="766" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/a5fea591-8380-408c-9ce3-b4d9ad50d40a">

<img width="550" alt="4" src="https://github.com/gilyeon00/TIL/assets/52391627/044b8470-5370-48e4-bc47-0b75f0f365e3">
