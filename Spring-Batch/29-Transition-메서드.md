# Transition

- Flow 내 Step 의 조건부 전환(전이)을 정의함
- Job 의 API 설정에서 on(String pattern) 메서드를 호출하면 TransitionBuilder 가 반환되어, Transition Flow 를 구성가능
- Step 의 종료상태(ExitStatus) 가 어떤 pattern 과도 매칭되지않으면, 스프링 배치에서 예외를 발생하고 해당 Job 은 실패로 종료
- Transition 은 구체적인 것부터 그렇지 않은 순서로 적용된다.

```java
@Bean
    public Job batchJob() {
        return jobBuilderFactory.get("batchJob")
                .start(step1())
                .on("COMPLETED")
								.to(step2())
                .from(step1())
                .on("FAILED")
								.to(step3())
                .end()
                .incrementer(new RunIdIncrementer())
                .build();
    }
```

## .on(String pattern)

- **TransitionBuilder** 변환
- 해당 Step1 의 StepExecution 의 ExitStatus 값과 일치하면, 그 다음으로 넘어갈 수 있음 (step2)
- Step 의 실행 결과로 돌려받는 종료상태(ExitStatus)와 매칭하는 패턴 스키마로서,  BatchStatus 와 매칭되는 것이 아니다.
- 가능한 특수문자
  - `*` : 0개 이상의 문자와 매칭, 모든 ExitStatus 와 매칭된다.
  - `?` : 정확히 1개의 문자와 매칭
  - EX) c*t ➡️ cat (o) count (o)
  - EX) c?t ➡️ cat (o) count (x)

## .to()

- 다음으로 실행할 단계를 지정한다.

## .from()

- 이전 단계에서 정의한 Transition 을 새롭게 추가 정의한다.

## Job 을 중단하거나, 종료하는 메서드

- Flow 가 실행되면 `FlowExecutionStatus` 에 상태값이 저장되고 최종적으로 Job 의 BatchStatus 와 ExitStatus 에 반영된다.
- Step 의 BatchStatus 및 ExitStatus 에는 아무런 영향을 주지않고, **Job 의 상태만을 변경**한다.

### .stop()

- FlowExecutionStatus 가 STOPPED 상태로 종료되는 transition
- Job 의 BatchStatus 와 ExitStatus 가 STOPPED 으로 종료됨

### .fail()

- FlowExecutionStatus 가 FAILED 상태로 종료되는 transition
- Job 의 BatchStatus 와 ExitStatus 가 FAILED 로 종료됨

### .end()

- FlowExecutionStatus 가 COMPLETED 상태로 종료되는 transition
- Job 의 BatchStatus 와 ExitStatus 가 COMPLETED 으로 종료됨
- Step 의 ExitStatus 가 FAILED 이더라도, Job 의 BatchStatus 가 COMPLETED 로 종료하도록 가능하며, 이 때 Job 의 재시작은 불가능
  - 원래는 Step 이 실패로 끝나면, Job 의 상태도 실패로 끝났다.

### .stopAndReStart(Step / Flow / JobExecutionDesicder)

- stop() transition 과 기본 흐름은 동일하다.
- 특정 step 에서 작업을 중단하도록 설정하면, 중단 이전의 Step 만 COMPLETED 으로 저장되고, 이후의 step 은 실행되지 않고 STOPPED 상태로 Job 이 종료된다.
- Job 이 다시 실행됐을 때 실행해야 할 Step 을 Restart 인자로 넘기면, 이전에 COMPETED 로 저장된 step 은 건너뛰고, 중단 이후 step 부터 시작한다.

![스크린샷 2023-10-12 오전 10.56.23.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/36094120-5f48-4363-8f9a-59cad0766f1e/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-12_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.56.23.png)

🍹 각각의 단위 Step 이나, Flow 같은 단위들 사이에서 어떤 조건에 따라 해당 액션을 취하는 부분들을 Transition 이라고 한다.

➡️ 이렇게 Flow 를 구성할 수 있다.

![스크린샷 2023-10-12 오전 10.56.59.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/6a8041ec-ff3e-4f0c-938d-8c6132e5639d/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-12_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.56.59.png)