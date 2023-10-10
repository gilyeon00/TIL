# Transition

- Flow 내 Step 의 조건부 전환(전이)을 정의함
- Job 의 API 설정에서 on(String pattern) 메서드를 호출하면 TransitionBuilder 가 반환되어, Transition Flow 를 구성가능
- Step 의 종료상태(ExitStatus) 가 어떤 pattern 과도 매칭되지않으면, 스프링 배치에서 예외를 발생하고 해당 Job 은 실패로 종료
- Transition 은 구체적인 것부터 그렇지 않은 순서로 적용된다.

# Transition-메서드

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