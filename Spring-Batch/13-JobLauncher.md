# JobLauncher

- 배치 Job을 실행시키는 역할
- Job 과 Job Parameters 를 인자로 받으며, 요청된 배치 작업을 수행한 후, 최종 client 에게 JobExecution을 반환한다.
- Spring boot batch 가 구동이 되면, JobLaunch Bean 이 자동 생성된다.
- Job 실행
    - JobLauncher.run(Job, JobParameters)
        - Spring boot batch 에서는 JobLauncherApplicationRunner 가 자동적으로 JobLauncer 을 실행시킨다.

          <img width="196" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/ba1d1651-850e-4a2b-b6b9-a24c06ff0ea2">

          batch job enabled : true (기본값)

    - **동기적 실행**
        - taskExecutor 를 `SyncTaskExecutor` 로 설정할 경우 (SyncTaskExecutor : 기본값) ⬅️  일반적인 방법
        - JobExecution  생성, JobExecution 을 획득하고, 배치 처리를 최종 완료한 이후 client 에게 JobExecution을 반환
        - 스케쥴러에 의한 배치처리에 적합함 ⬅️ 배치 처리시간이 길어져도 상관이 없는 경우
    - **비동기적 실행**
        - taskExecutor 가 `SimpleAsyncTaskExecutor` 로 설정할 경우
        - JobExecution  생성, JobExecution 을 획득한 후, client 에게 바로 JobExecution 을 반환하고, spring batch 가 내부적으로 배치 처리를 완료한다
        - HTTP 요청에 의한 배치처리에 적함함 ⬅️ 배치 처리시간이 길 경우 응답이 늦어지지 않도록 함


## 동기적 실행

<img width="1221" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/966a1d13-9f86-4bff-951f-8de5ca129ba9">

```jsx
@RestController
@RequiredArgsConstructor
public class JobLauncherController {

    private final Job job;
    private final JobLauncher jobLauncher;

    @PostMapping("/batch")
    public String launch(@RequestBody MemberDto memberDto) throws JobInstanceAlreadyCompleteException, JobExecutionAlreadyRunningException, JobParametersInvalidException, JobRestartException {

        JobParameters jobParameters = new JobParametersBuilder()
                .addLong("id", memberDto.getId())
                .addDate("date", new Date())
                .toJobParameters();

        jobLauncher.run(job, jobParameters);

        return "batch completed ======== !";
    }
}
```

<img width="815" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/49e5298c-74fe-4174-8d05-6da95cfec243">

- SimpleJobLauncher 의 기본 리턴 값은 SyncTaskExecutor 이다. (동기적 실횅 방식)

SimpleJobLauncher 는 Bean으로 생성 되지않는다

## 비동기적 실행

<img width="1207" alt="4" src="https://github.com/gilyeon00/TIL/assets/52391627/daadd054-e58a-435f-8f70-dfbd8d4221f3">

```jsx
@RestController
@RequiredArgsConstructor
public class JobLauncherController {

    private final Job job;
    private final JobLauncher jobLauncher;
    private final BasicBatchConfigurer basicBatchConfigurer;

    @PostMapping("/batch")
    public String launch(@RequestBody MemberDto memberDto) throws JobInstanceAlreadyCompleteException, JobExecutionAlreadyRunningException, JobParametersInvalidException, JobRestartException {

        JobParameters jobParameters = new JobParametersBuilder()
                .addLong("id", memberDto.getId())
                .addDate("date", new Date())
                .toJobParameters();

        SimpleJobLauncher jobLauncher1 = (SimpleJobLauncher) basicBatchConfigurer.getJobLauncher();
        jobLauncher1.setTaskExecutor(new SimpleAsyncTaskExecutor());
        jobLauncher1.run(job, jobParameters);
        return "batch completed ======== !";
    }
}
```