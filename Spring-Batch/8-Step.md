# Step

- Batch Job을 구성하는 독립적인 하나의 단계로서, 실제 배치 처리를 정의하고 컨트롤하는데 필요한 모든 정보를 가지고 있는 객체
- 단순한 단일 task 뿐만 아니라, 입력과 처리, 출력과 관련된 모든 복잡한 비즈니스 로직을 포함하는 설정들을 가지고 있음
- 배치 작업을 어떻게 구성하고, 실행할 것인지 Job 의 세부 작업을 Task 기반으로 설정하고 명세해 놓은 객체
- 모든 Job은 하나 이상의 step으로 구성됨

<aside>
💡 Job 은 Step 들을 명세해놓은 틀 ! 
실질적인 로직은 Step 이 담당한다.

</aside>

# Step 의 구현체

- TaskletStep
    - 가장 기본이 되는 클래스로서, Tasklet 타입의 구현체들을 제어
- PartitionStep
    - 멀티 스레드 방식으로 Step을 여러 개로 분리해서 실행
- JobStep
    - Step 내에서 Job을 실행
- FlowStep
    - Step 내에서 Flow를 실행

# Step 도메인의 이해
<img width="1236" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/610ab692-d039-4257-b7d0-e4dbbd44fc43">

- Step.`execute`(StepExecution stepExecution) : Step을 실행시키는 메서드
    - `StepExecution` : Step의 실행 결과 상태 저장
- Step 이 실행완료 된 후, 재실행은 기본적으로 false
    - true 로 설정하면 → 실행 완료돼도 재실행 가능

# Step 생성 방법

### 1️⃣ Tasklet을 직접 생성해서 TaskletStep 생성

```jsx
@Bean
    public Step step1 () {
        return stepBuilderFactory.get("step1")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("------step1 has executed------");
                        return RepeatStatus.FINISHED;
                    }
                })
                .build();
    }
```

- myTasklet 이라는 Tasklet 직접 생성

    ```jsx
    @Bean
        public Step step1 () {
            return stepBuilderFactory.get("step1")
                    .tasklet(myTasklet())
                    .build();
        }
    ```


### 2️⃣ Chunk 기반의 TaskletStep 생성

```jsx
@Bean
    public Step step2 () {
        return stepBuilderFactory.get("step2")
                .<Member, Member> chunk(100)
                .reader(myReader())
                .writer(myWriter())
                .build();
    }
```

- Chunk 기반의 작업을 할 수 있도록, Spring Batch에서 내부적으로 `ChunkOrientedTasklet` 을 만들어놓음
- `ChunkOrientedTasklet` 이를 기반으로 Step을 생성함
    - 내부적으로 ItemReader, ( *ItemProcessor )*, ItemWriter,  을 사용해서 Step 수행

### 3️⃣ JobStep

```jsx
@Bean
    public Step step3 () {
        return stepBuilderFactory.get("step3")
                .job(job2())
                .launcher(jobLauncher)
                .parametersExtractor(jobParameterExractor())
                .build();
    }
```

- Step 에서 Job을 실행시키는 방법
1. Job 설정
2. Job 을 실행시키는 JobLauncher 설정
3. Job 에 필요한 parameter 설정

### 4️⃣ FlowStep

```jsx
@Bean
    public Step step4 () {
        return stepBuilderFactory.get("step4")
                .flow(myFlow())
                .build();
    }
```

- Step 에서 Flow 를 실행