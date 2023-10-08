# FlowJob()-start()-next()

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

## .start(step1())

- 처음 실행할 Flow 를 설정

## .next()