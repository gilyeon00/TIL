# JobBuilderFactory

- JobBuilder 를 생성하는 팩토리 클래스로서, get(String name) 메서드 제공
    - jobBuilderFactory.get(”jobName”)
        - ”jobName” 은 스프링 배치가 Job을 실행시킬 때 참조하는 Job의 이름

# JobBuilder

- Job 을 구성하는 설정 조건에 따라 두 개의 하위 빌더 클래스를 생성하고, 실제 Job 생성을 위임한다
- JobBuilder 가 생성될 때, JobBuilderFactory 로부터 JobRepository 도 같이 전달받는다. ➡️ 그래야 Job 이 수행될 때 메타 데이터가 jobRepository 를 통해 DB 에 저장될 수 있음

## SimpleJobBuilder

- SimpleJob 을 생성하는 Builder 클래스
- Job 실행과 관련된 여러 설정 API 를 제공

## FlowJobBuilder

- FlowJob 을 생성하는 Builder 클래스
- 내부적으로 FlowBuilder 를 반환함으로써, Flow 실행과 관련된  여러 설정 API 를 제공

---

# JobBuilderFactory 의 시나리오

![스크린샷 2023-09-21 오후 10.50.32.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/3b7e14cb-7e7e-420d-986f-a216143b6e7f/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.50.32.png)

get(jobName) 을 통해 JobBuilderFactory 가 JobBuilder 를 생성한다.

1. `SimpleJobBuilder`
    1. JobBuilder 가 start(step) 을 실행한다면, SimpleJobBuilder 를 생성

       ![스크린샷 2023-09-21 오후 11.20.45.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/deb538c5-a063-433a-a002-17bffd4f8283/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.20.45.png)

2. `FlowJobBuilder`
    1. JobBuilder 가 start(flow) 을 실행한다면, FlowJobBuilder 를 생성
    2. JobBuilder 가 flow(step) 을 실행한다면, FlowJobBuilder 를 생성
    3. FlowJobBuilder 는 내부적으로 start(flow) / start(step) 을 통해 JobFlowBuilder 를 생성
        1. FlowJobBuilder : Job 생성
        2. JobFlowBuilder : Flow 생성

       ![스크린샷 2023-09-21 오후 11.21.09.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/3f59ebb1-8121-4d40-af70-aeff33844c34/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.21.09.png)
    ```java
        @Bean
            public Flow flow() {
                FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("my-flow");
                flowBuilder
                        .start(step1())
                        .next(step2())
                        .end();
        
                return flowBuilder.build();
            }
    ```
    
## 🧐start 를 flow 로 하지 않고, 중간에 넣으면 에러 발생
    
```java
    @Bean
        public Job job2 () {
            return jobBuilderFactory.get("my-job2")
                    .start(step3())
                    .next(flow())
                    .end()
                    .build();
        }
```
    
➡️flow() 의 반환객체를 JobExecutionDecider 로 변경해주면, 에러가 나지 않는다. 
    
```java
    @Bean
        public JobExecutionDecider flow() {
            FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("my-flow");
            flowBuilder
                    .start(step1())
                    .next(step2())
                    .end();
    
            return (JobExecutionDecider) flowBuilder.build();
        }
```

⭐️ start() 를 통해, **JobBuilderFactory** 가 SimpleJobBuilder 를 만들지, FlowJobBuilder 를 만들지 결정한다.
start() 에 Step 을 전달할 경우 `SimpleJobBuilder` 가 만들어지는데, SimpleJobBuilder 는 무조건 step 만을 가져야하므로 에러가 난다.
반면, `FlowJobBuilder` 같은 경우 step/flow 를 다 받을 수 있다.
따라서, start() 에 Flow 를 전달하면 FlowJobBuilder 가 만들어지므로, next() 에 Step / Flow 아무거나 넣어도 무방하다.

- SimpleJobBuilder 에서 Flow를 `next()`에 연결하려면, 해당 Flow를 다른 Step 또는 JobExecutionDecider로 감싸서 사용해야 한다.