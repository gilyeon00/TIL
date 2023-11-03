# FlowStep

- Step 내에 Flow 를 할당하여 실행시키는 도메인 객체
- flowStep - FlowExecutionStatus 의 BatchStatus 와 ExitStatus 는 Flow 의 최종 상태값에 따라 결정된다.
- Job > flowStep > Flow > Step 구조
- 생성 : StepBuildeFactory > StepBuilder > FlowStepBuilder > FlowStep
- **기본 구조**

    ```java
        @Bean
        public Step flowStep() {
            return stepBuilderFactory.get("flowStep")
                    .flow(flowA())
                    .build();
        }
    ```

- **구성**

```java
   @Bean
    public Job flowStepJob() {
        return jobBuilderFactory.get("flowStepJob")
                .start(flowStep())
                .next(step3())
                .incrementer(new RunIdIncrementer())
                .build();
    }

    @Bean
    public Step flowStep() {
        return stepBuilderFactory.get("flowStep")
                .flow(myflow())
                .build();
    }

    @Bean
    public Flow myflow() {
        FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("myflow");
        flowBuilder
                .start(step2())
                .end();
        return flowBuilder.build();
    }
```

## JobStep 과의 비교

[JobStep](https://github.com/gilyeon00/TIL/blob/main/Spring-Batch/25-JobStep.md)

- JobStep 은 Step 내에서 Job 을 가지고 있는 형태이다
- Step 이긴하나, 내부에 Job이 있음
- Job - Step - Job - Step 구조

```java
	  @Bean
    public Job parentJob() {
        return jobBuilderFactory.get("parentJob")
                .start(jobStep())
                .next(step2())
                .incrementer(new RunIdIncrementer())
                .build();
    }

    @Bean
    public Step jobStep () {
        return stepBuilderFactory.get("my-jobStep")
                .job(childJob())
                .build();
    }

    @Bean
    public Job childJob() {
        return jobBuilderFactory.get("childJob")
                .start(step1())
                .incrementer(new CustomJobParametersIncrementer())
                .build();
    }
```

---

- StepBuilderFactory > StepBuilder

`stepBuilderFactory.get("Step")` 이후에 메서드 체이닝으로 붙는 메서드에 따라 하위빌더가 생성된다.

![1](https://github.com/gilyeon00/TIL/assets/52391627/cb14a531-57a2-4e3a-b4ff-3438fb60c3f5)

![2](https://github.com/gilyeon00/TIL/assets/52391627/c3776363-17f9-40a2-9ffc-145c7d51dfa2)