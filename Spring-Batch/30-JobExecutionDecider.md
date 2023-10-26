# JobExecutionDecider

- ExitStatus 를 조작하거나 StepExecutionListener 를 등록할 필요없이 **Transition 처리를 위한 전용 클래스**
    - 여러 Step 들을 많은 조건에 따른 흐름에 구성하다보면 가독성이 떨어질 수 있음
        - on, to, end 와 같은 메서드가 복잡하게 구성이되면, 강한 결합이 생김
    - ➡️ **JobExecutionDecider 안에서 자체적으로 조건에 따라 흐름을 정의할 수 있음**
- Step 과 Transition 역할을 명확히 분리해서 설정할 수 있음
- Step 의 ExitStatus 가 아닌 **JobExecutionDecider** 의 `FlowExecutionStatus` **상태값을 새롭게 설정해서 반환함**
    - JobFlow 같은 경우 Step 의 ExitStatus 값이 FlowExecutionStatus 값으로 반영된 다음, 다시 JobFlow 값으로 반영이됨
        - ➡️ **Step 의 ExitStatus 값에 따라 FlowExecutionStatus 값이 정해짐 ??**
- **Custom** `JobExecutionDecider` 은  *`JobExecutionDecider` 를 상속받아 구현해줘야한다.*

```java
@Bean
    public Job deciderJob(){
        return jobBuilderFactory.get("deciderJob")
                .start(step1())
                .next(decider())
                .from(decider()).on("ODD").to(oddStep())
                .from(decider()).on("EVEN").to(evenStep())
                .end()
                .build();
    }

    @Bean
    public JobExecutionDecider decider() {
        return new OddDecider();
    }

    public static class OddDecider implements JobExecutionDecider{
        private int count = 0;
        @Override
        public FlowExecutionStatus decide(JobExecution jobExecution, StepExecution stepExecution) {
            count ++;
            if(count % 2 == 0){
                return new FlowExecutionStatus("EVEN");
            } else {
                return new FlowExecutionStatus("ODD");
            }
        }
    }

@Bean
    public Step evenStep(){
        return stepBuilderFactory.get("evenStep")
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("========evenStep executed==========");
                    return RepeatStatus.FINISHED;
                }))
                .build();
    }

    @Bean
    public Step oddStep(){
        return stepBuilderFactory.get("oddStep")
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("========oddStep executed==========");
                    return RepeatStatus.FINISHED;
                }))
                .build();
    }

    @Bean
    public Step step1(){
        return stepBuilderFactory.get("step1")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("==============step1 executed=============");
                        return RepeatStatus.FINISHED;
                    }
                })
                .build();
    }
```

---

# 🤡정리

- **분기 처리 시, 상태값을 커스텀하게 설정할 수 있다.**
    - COMPELETED / FAILED 이외에, ODD / EVEN .. 등 에 따른 분기 설정 가능
- **JobExecutionDecider 클래스 내부에서 커스텀하게 조건에 따라 흐름 정의 가능**
    - from(), on(), stop().. 등 제공되는 메서드가 아닌, 자바 문법으로 흐름 정의 가능
- JobExecutionDecider 을 사용해서 하고자하는 어떤 조건적인 흐름을 제어할 수 있다.
  하지만 앞서 했던 Transition 메서드를 사용해서 하는 것도 같은 방법이다.
  이는 Job 을 구성하는 정책이나 방향에 따라 결정하는 것으로 좋고 나쁜 방법은 없다.