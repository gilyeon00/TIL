# FlowJob

- Step 을 순차적으로만 구성하는 것이 아닌, 특정한 상태에 따라 흐름을 전환하도록 구성할 수 있으며, FlowJobBuilder 에 의해 생성된다.
    - Step 이 실패하더라도 Job 은 실패로 끝나야하지 않는 경우
    - Step 이 성공했을 때 다음에 실행해야 할 Step 을 구분해서 실행해야하는 경우
    - 특정 Step 은 전혀 실행되지 않게 구성해야하는 경우
- Flow 와 Job 의 흐름을 구성하는 데만 관여하고, 실제 비즈니스 로직은 Step 에서 이뤄진다.
- 내부적으로 SimpleFlow 객체를 포함하고 있으며, Job 실행 시 호출한다.

```java
//    step1 이 성공하면 step2 실행
//    step1 이 실패하면 step3 실행
    @Bean
    public Job batchJob() {
        return jobBuilderFactory.get("batchJob")
                .start(step1())
                .on("COMPLETED").to(step2())
                .from(step1())
                .on("FAILED").to(step3())
                .end()
                .incrementer(new RunIdIncrementer())
                .build();
    }
```

![8](https://github.com/gilyeon00/TIL/assets/52391627/ef9957a2-13f3-4ec2-83b3-be3811b08fce)