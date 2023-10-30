## Case 1.

다음 Job 에서 Step1 - Completed, Step4 - Failed 로 종료된다면,

해당 Job 과 Step4   Status 와 ExitCode 는 각각 무엇일까?

```jsx
@Bean
    public Job batchJob() {
        return jobBuilderFactory.get("batchJob")
                .start(step1())
                    .on("FAILED")
                    .to(step2())
                    .on("*")
                    .stop()
                .from(step1())
                    .on("*")
                    .to(step3())
                    .next(step4())
                    .on("FAILED")
                    .end()
                .end()
                .incrementer(new RunIdIncrementer())
                .build();
    }
```

[ **정답 ]**

- **Job**
    - Status : Completed
    - ExitCode : Completed
- **Step4**
    - Status : Completed
    - ExitCode : Failed

우리는 이전에 Step 이 FAILED 로 끝나더라도, **의도한 결과였다면 Job 의 ExitCode 는 Completed** 로 끝난다는 것을 알았다. [Transition-메서드 ](https://www.notion.so/Transition-b91e2f7bdab14330bb66fcd471a9ccd1?pvs=21)

---

## Case 2.

하지만 `Step1 - Failed`, `Step2 - Completed` 로 종료됐을 때 이때의 Job 과 Step 2의 Status 와 ExitCode 는 각각 어떻게 될까?

```jsx
@Bean
    public Job exitStatusExampleJob() {
        return jobBuilderFactory.get("exitStatusExampleJob")
                .start(step1())
                    .on("FAILED")
                    .to(step2())
                    .on("PASS")
                    .stop()
                .end()
                .incrementer(new RunIdIncrementer())
                .build();
    }

@Bean
    public Step step1() {
        return stepBuilderFactory.get("step1")
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println(">>>> step1 executed");
                    contribution.getStepExecution().setExitStatus(ExitStatus.FAILED);
                    return RepeatStatus.FINISHED;
                }))
                .build();
    }

    @Bean
    public Step step2() {
        return stepBuilderFactory.get("step2")
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println(">>>> step2 executed");
                    return  RepeatStatus.FINISHED;
                }))
                .build();
    }
```

[ **정답 ]**

- **Job**
    - Status : Failed
    - ExitCode : Failed
      <img width="562" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/a8019f98-5f4a-431b-85ca-7c1b8591b12e">


- **Step2**
    - Status : Completed
    - ExitCode : Completed
      <img width="451" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/a4e7457a-c0d1-4cb0-8088-1036227c3ace">


**중요한건 의도한대로 Status 와 ExtiCode 가 정해진다는 것이다 !**

ㄴ 의도한 것은 PASS 일때 Stop 된다는 거지, Completed 일때는 따로 지정을 안해줬기때문 !

---

### 🥕 해결방법 - 사용자 정의 ExitCode 만들기

**Step2 에 Listener 설정**

```jsx
@Bean
    public Step step2() {
        return stepBuilderFactory.get("step2")
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println(">>>> step2 executed");
                    return  RepeatStatus.FINISHED;
                }))
                .listener(new PassCheckListener())
                .build();
    }
```

**StepExecutionListener 를 상속받는 Listener Class 생성**

```jsx

public class PassCheckListener implements StepExecutionListener {
    @Override
    public void beforeStep(StepExecution stepExecution) {

    }

    @Override
    public ExitStatus afterStep(StepExecution stepExecution) {
        String exitCode = stepExecution.getExitStatus().getExitCode();
        if (!exitCode.equals(ExitStatus.FAILED.getExitCode())){
            return new ExitStatus("PASS");
        }
        return null;
    }
}
```

Listener 를 통해서 StepExecution 이 실행되고나서,
ExitCode 가 FAILED 가 아닐때 PASS 로 ExitStatus 를 지정해준다.

그러면 Job 에서는 Step2 의 ExitCode 가 Pass 이므로, Stopped 로 끝난것을 볼 수 있다(Stop 으로 설정했기 때문 !)

<img width="701" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/ab7e7fbb-0eb5-4c91-9f60-ce4814646cb5">

<img width="858" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/92940a63-b770-43f6-b29a-addc2d051b6a">