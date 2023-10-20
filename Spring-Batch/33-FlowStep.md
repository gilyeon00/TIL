# FlowStep

- Step 내에 Flow 를 할당하여 실행시키는 도메인 객체
- flowStep 의 BatchStatus 와 ExitStatus 는 Flow 의 최종 상태값에 따라 결정된다.

```java
@Bean
    public Step flowStep() {
        return stepBuilderFactory.get("flowStep")
                .flow(flowA())
                .build();
    }
```