# SimpleFlow

- 스프링 배치에서 제공하는 Flow 의 구현체로서, 각 요소(`Step`, `Flow`, `JobExecutionDecider`) 들을 담고 있는 State 를 실행시키는 도메인 객체
  - State 안에 `Step`, `Flow`, `JobExecutionDecider` 가 있는 형태로서, SimpleFlow 는 State 를 실행 시킴으로서 각 요소를 실행시킨다.
- SimpleFlow 는 FlowBuilder 를 사용해서 생성하며 Transition 과 조합하여 여러개의 Flow 및 중첩 Flow 를 만들어 Job 구성 가능
  - JobBuilderFactory > FlowJobBuilder > FlowBuilder > SimpleFlow
- SimpleFlow 안에서 Step 뿐만 아니라, Flow 를 중첩되게 구성 가능하다
- 아래 코드도 SimpleFlow 가 중첩되어있다.
  - start() ~ end() 를 통해 SimpleFlow 를 생성하였고,  그 안에서 myFlow1()

```java
    @Bean
    public Job myjob(){
        return jobBuilderFactory.get("myFlowJob")    JobBuilder
                .start(myFlow1())   JobFlowBuilder
                .on("COMPLETED").to(myFlow2())    FlowBuilder<FlowJobBuilder>
                .next(step3())
                .end()       FlowJobBuilder
                .build();    FlowJob
    }

		@Bean
    public Flow myFlow1(){
        FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("myFlow");
        flowBuilder.start(step1())
                .next(step2())
                .end();

        return flowBuilder.build();     SimpleFlow 반환
    }
```

---

## 실습 😼

- 다음과 같은 아키텍처의 Job 을 만들기
- 다음과 같은 Transition 을 가지기
  - Flow 1 ▶️성공
    - Flow 2
  - Flow 1 ▶️실패
    - Flow 3

<img width="173" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/22471ab2-b2e3-4a66-a4f5-2357ef5a8bd6">

```java
@Bean
    public Job flowJob(){
        return jobBuilderFactory.get("myFlowJob")
                .start(myFlow1())
                    .on("COMPLETED")
                    .to(myFlow2())
                .from(myFlow1())
                    .on("FAILED")
                    .to(myFlow3())
                .next(step3())
                .end()
                .build();
    }

    @Bean
    public Flow myFlow1(){
        FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("myFlow");
        flowBuilder.start(step1())
                .next(step2())
                .end();

        return flowBuilder.build();
    }

    @Bean
    public Flow myFlow2(){
        FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("myFlow2");
        flowBuilder.start(myFlow3())
                .next(step5())
								.next(step6())
                .end();

        return flowBuilder.build();
    }

    @Bean
    public Flow myFlow3(){
        FlowBuilder<Flow> flowBuilder = new FlowBuilder<>("myFlow2");
        flowBuilder.start(step3())
                .next(step4())
                .end();

        return flowBuilder.build();
    }
```