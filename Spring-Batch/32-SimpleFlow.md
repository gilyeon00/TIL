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
        return jobBuilderFactory.get("myFlowJob")    JobBuilder 반환
                .start(myFlow1())   JobFlowBuilder 반환
                .on("COMPLETED").to(myFlow2())    FlowBuilder<FlowJobBuilder> 반환
                .next(step3())
                .end()       FlowJobBuilder 반환
                .build();    FlowJob 반환
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