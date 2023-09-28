# StepBuilderFactory

- StepBuilder 를 생성하는 팩토리 클래스로서, get(String name) 메서드 제공
- StepBuilderFactory.get(”stepName”)
  - stepName 으로 Step을 생성한다.

# StepBuilder

- Step 을 구성하는 설정 조건에 따라 5개의 하위 빌더 클래스를 생성하고, 실제 Step 생성을 위임한다.
- StepBuilder 에서 메서드 타입에 따라 하위 빌더가 생성됨
- JobRepository 는 빌더 클래스를 통해 Step 객체에 전달되어, 메타 데이터를 기록하는데 사용된다.

### TaskletStepBuilder

- tasklet( tasklet() )
- TaskletStep 을 생성하는 기본 빌더 클래스

### SimpleStepBuilder

- chunk( chunkSize )
- chunk ( completionPolicy )
- TaskletStep 을 생성하며, 내부적으로 청크기반의 작업을 처리하는 ChunkOrientedTasklet 클래스를 생성한다.

    ```java
    @Bean
        public Step step2 () {
            return stepBuilderFactory.get("step2")
                    .<String, String>chunk(3)
                    .reader(new ItemReader<String>() {
                        @Override
                        public String read() throws Exception, UnexpectedInputException, ParseException, NonTransientResourceException {
                            return null;
                        }
                    })
                    .processor(new ItemProcessor<String, String>() {
                        @Override
                        public String process(String item) throws Exception {
                            return null;
                        }
                    })
                    .writer(new ItemWriter<String>() {
                        @Override
                        public void write(List<? extends String> items) throws Exception {
                            
                        }
                    })
                    .build();
        }
    ```


### PartitionStepBuilder

- partitioner( stepName, partitioner )
- partitioner( Step step )
- PartitionStep 을 생성하며, 멀티 스레드 방식으로 Job 을 실행한다.
  - step을 여러개로 분리해서, 동시적으로 실행할 수 있는 기능

```java
@Bean
    public Step step1 () {
        return stepBuilderFactory.get("step1")
                .partitioner(step2())
                .gridSize(2)
                .build();
    }
```

### JobStepBuilder

- job( Job job )
- JobStep 을 생성하여, Step 안에서 Job을 생성한다.

### FlowStepBuilder

- flow( Flow flow )
- FlowStep 을 생성하여, Step 안에서 Flow 를 실행한다.