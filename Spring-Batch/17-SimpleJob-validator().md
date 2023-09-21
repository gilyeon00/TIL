# SimpleJob-validator()

- Job 실행에 꼭 필요한 파라미터를 검증하는 용도
- JobParameters 값을 매개 변수로 받아 검증함
    - 해당 Job 을 실행하는 시점에, <Key, Value> 로 구성된 JobParameter 가 올바른지 검증
- DefaultJobParametersValidator 구현체를 지원하며, 좀 더 복잡한 제약조건이 있다면 인터페이스를 직접 구현할 수도 있음

```java
@Bean
    public Job job1 () {
        return jobBuilderFactory.get("jobb1")
                .start(step1())
                .next(step2())
                .validator(new JobParametersValidator() {
                    @Override
                    public void validate(JobParameters parameters) throws JobParametersInvalidException {

                    }
                })
                .build();
    }
```