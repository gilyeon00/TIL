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

### .on(String pattern)

- **TransitionBuilder** 변환
- 해당 Step1 의 StepExecution 의 ExitStatus 값과 일치하면, 그 다음으로 넘어갈 수 있음 (step2)
- Step 의 실행 결과로 돌려받는 종료상태(ExitStatus)와 매칭하는 패턴 스키마로서,  BatchStatus 와 매칭되는 것이 아니다.
- 가능한 특수문자
  - `*` : 0개 이상의 문자와 매칭, 모든 ExitStatus 와 매칭된다.
  - `?` : 정확히 1개의 문자와 매칭
  - EX) c*t ➡️ cat (o) count (o)
  - EX) c?t ➡️ cat (o) count (x)

### .to()

- 다음으로 실행할 단계를 지정한다.

### .from()

- 이전에 실행했던 단위/항목을 재선언할 때 사용한다.

---

## Job 을 중단하거나, 종료하는 메서드

- Flow 가 실행되면 `FlowExecutionStatus` 에 상태값이 저장되고 최종적으로 Job 의 BatchStatus 와 ExitStatus 에 반영된다.
- ⭐️ Step 의 BatchStatus 및 ExitStatus 에는 아무런 영향을 주지않고, **Job 의 상태만을 변경**한다.

### .stop()

- `FlowExecutionStatus` 가 STOPPED 상태로 종료되는 transition
- **Job** 의 `BatchStatus` 와 `ExitStatus` 가 STOPPED 으로 종료됨

### .fail()

- `FlowExecutionStatus` 가 FAILED 상태로 종료되는 transition
- **Job** 의 `BatchStatus` 와 `ExitStatus` 가 FAILED 로 종료됨

### .end()

- FlowExecutionStatus 가 COMPLETED 상태로 종료되는 transition
- Job 의 BatchStatus 와 ExitStatus 가 COMPLETED 으로 종료됨
- Step 의 ExitStatus 가 FAILED 이더라도, Job 의 BatchStatus 가 COMPLETED 로 종료하도록 가능하며, 이 때 Job 의 재시작은 불가능
  - 원래는 Step 이 실패로 끝나면, Job 의 상태도 실패로 끝났다.
- end() 를 해야만, Flow 객체가 생성된다.

### .stopAndReStart(Step / Flow / JobExecutionDesicder)

- stop() transition 과 기본 흐름은 동일하다.
- 특정 step 에서 작업을 중단하도록 설정하면, 중단 이전의 Step 만 COMPLETED 으로 저장되고, 이후의 step 은 실행되지 않고 STOPPED 상태로 Job 이 종료된다.
- Job 이 다시 실행됐을 때 실행해야 할 Step 을 Restart 인자로 넘기면, 이전에 COMPETED 로 저장된 step 은 건너뛰고, 중단 이후 step 부터 시작한다.

![1](https://github.com/gilyeon00/TIL/assets/52391627/caa73a14-88be-46e1-868e-9082056d8c32)

🍹각각의 단위 Step 이나, Flow 같은 단위들 사이에서 어떤 조건에 따라 해당 액션을 취하는 부분들을 Transition 이라고 한다.

➡️이렇게 Flow 를 구성할 수 있다.

![2](https://github.com/gilyeon00/TIL/assets/52391627/a9b9aee1-17d8-4013-b837-b27512004c2a)

---

## 실습 🍷
A. Step1 가 FAILED 로 종료되면 Step2 를 실행
  1. Step2 가 성공적으로 완료하면, Step2 의 종료상태와 상관없이 Job 중단
. Step1 가 FAILED 가 아닌 모든 경우 Step3 을 실행
  1. Step3 이 성공적으로 완료되면, Step4 를 실행
  2. Step4 의 종료상태가 FAILED 면 Job 종료

---

```java
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

1. **Step1 가 FAILED 로 종료되면 Step2 를 실행**
  1. Step1 강제 FAILED 설정

![스크린샷 2023-10-20 오전 10.27.10.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/71d3fcac-7064-4bb7-8412-6a048eb61857/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-20_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.27.10.png)

![스크린샷 2023-10-20 오전 10.27.30.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/c52b1168-517a-465a-84ba-813be6be5a28/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-20_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.27.30.png)

- Step1 이 FAILED 이므로, start 구문이 실행되고, from 구문은 실행되지 않는다
- Step1 은 FAILED 이므로, EXIT_CODE 는 FAILED 이다.
- 하지만, 이것은 의도한대로 나오는 결과이므로 STATUS 는 COMPLETED 이다.
- start 구문은 stop 구문을 사용했기때문에, 해당 Job 은 STOPPED 로 종료상태가 지정된다.

![스크린샷 2023-10-20 오전 10.33.28.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/074bcc03-a8c4-4b74-a530-1ecefc49a649/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-20_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.33.28.png)

1. **Step1 가 FAILED 가 아닌 모든 경우 Step3 을 실행**
  1. Step1 FAILED 설정 제거, Step4 강제 FAILED 설정

![스크린샷 2023-10-20 오전 10.32.31.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/d3ef6cbc-0032-4dfc-852a-e77e5daaa1a6/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-20_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.32.31.png)

![스크린샷 2023-10-20 오전 10.32.43.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/6a6b4d6a-6ef0-4ed8-b4b4-fe1ce36c8fbd/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-20_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.32.43.png)

- Step1 이 COMPLETED 이므로, start 구문이 실행되지않고, from 구문이 실행된다.
- Step4 는 FAILED 이므로, EXIT_CODE 는 FAILED 이다. 마찬가지로, 의도된 결과여서 STATUS 는 COMPLETED 이다.
- from 구문은 stop 을 설정해주지 않았기때문에, 해당 Job 은 COMPLETED 로 종료상태가 저장된다.
  - 이 Job 두번 다신 재실행하지 못한다.

![스크린샷 2023-10-20 오전 10.37.52.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/c659b4bb-6091-4e73-a6fa-3869f5629b00/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-20_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.37.52.png)