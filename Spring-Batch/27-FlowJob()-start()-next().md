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

## .start(flowA())

- 처음 실행할 Flow 를 설정
- 만약 `Step` 으로 시작한다면, next 에 `Flow` 는 들어갈 수 없다.

  ![스크린샷 2023-10-19 오후 1.37.08.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/29699dcf-a101-459e-8005-3f8e6c120536/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-10-19_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_1.37.08.png)


## .next()

- 다음에 실행할 Flow / Step 설정

```java
@Bean
    public Flow flowA() {
        FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("flowA");
        flowBuilder.start(step1())
                .next(step2())
                .end();
        return flowBuilder.build();
    }
```