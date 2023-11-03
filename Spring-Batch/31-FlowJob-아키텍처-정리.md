# FlowJob 아키텍처

[SimpleJob 아키텍처](https://github.com/gilyeon00/TIL/blob/main/Spring-Batch/20-SimpleJob-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EC%A0%95%EB%A6%AC.md)

우리는 이전에 SimpleJob 을 공부했었다. FlowJob 은 SimpleJob 에 비해서 더 복잡한 구성을 하고 있기때문에, SimpleJob 을 완벽히 숙지하는게 중요하다 !

## FlowJob

- FlowJob 도 SimpleJob 과 마찬가지로,  JobLauncher 에 의해 실행된다.
    - JobParameters 와 FlowJob 을 이용해 JobLauncher로부터 실행됨
- 실행되면 SimpleJob 과 마찬가지로 JobInstance, JobExecution 생성된다.
    - 이때의 배치 상태(ExecutionContext 의 BatchStatus) 는 STARTED

## FlowExecutor

- **Flow 를 실행시키는 객체**, FlowExecutor 통해 실제 Flow 가 실행된다.
    - **FlowExecution 객체를 만든다 !**
        - **FlowExecution** : JobExecution, StepExecution 처럼 Flow 를 실행시키면서 발생하는 정보들을 담고있는 Flow 실행객체
- 이때 JobListener 가 등록되면, JobListener 에 해당하는 BeforeJob이 이때 호출된다.
- **SimpleFlow 를 실행시킨다.**

## SimpleFlow

- 사실상 우리가 구성한 FlowJob 의 흐름을 핸들링하는 주체
- State 라는 속성이 있다.
    - 내부에 Step, Flow, Decide 타입의 객체가 있다. ⬅️ FlowJob 을 구성할 때 사용한 항목
    - 이 객체들을 가지고 있으면서, 각각의 항목을 실행시키는 역할을 한다.
- 모든 항목의 실행을 마치게되면, `FlowExecutionStatus` 라는 상태정보를 가진 값 반환
    - FlowJob 의 최종적인 상태
    - State ▶️ SimpleFlow

![1](https://github.com/gilyeon00/TIL/assets/52391627/f7cb4057-206a-412e-a8fe-c1ec871240bd)
---
---

## Job

- Flow 는 start() 에만 들어갈 수 있다.
  - start()에 Step 이 들어갈 경우, next() 에 Flow 는 들어갈 수 없다.
  - start(Step step) ➡️ SimpleJobBuilder
    - SimpleJobBuilder 는 Step 만 가능
  - start(Flow flow) ➡️ JobFlowBuilder
    - JobFlowBuilder 는 Flow / Step 둘 다 가능
    - [JobBuilderFactory](https://github.com/gilyeon00/TIL/blob/main/Spring-Batch/15-JobBuilderFactory.md)

```java
 		@Bean
    public Job myjob(){
        return jobBuilderFactory.get("myFlow")
                .start(myFlow())
                .next(step3())
                .end()
                .build();
    }
```

## SimpleFlow

- Flow 는 FlowBuilder 를 통해서 만들 수 있다.

```java

    @Bean
    public Flow myFlow(){
        FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("myFlow");
        flowBuilder.start(step1())
                .next(step2())
                .end();

        return flowBuilder.build();
    }
```