# ExecutionContext

- Job과 Step에 포함된 도메인은 아니고, 관련이 있는 도메인
- Job 재시작시 이미 처리한 Row 데이터는 건너뛰고, 이후부터 수행하도록 할 때 상태 정보를 활용한다.
    - 왜? - 만약에 중간에 중단/실패되더라도, 공유 정보를 통해 수행할 수 있도록
- 프레임워크에서 유지 및 관리하는 key/value 으로 된 컬렉션 (일종의 map)
    - DB 에 직렬화 한 값으로 저장됨 { “key” : “value” }
- StepExecution 또는 JobExecution 객체의 상태(속성)를 저장하는 `공유 객체`
    - 공유 객체 : 여러 객체가  ExecutionContext 참조할 수 있다.
    - Job / Step 이 실행하면서, 중간 중간 리스너 등을 이용해서 ExecutionContext 객체의 key에 맞는 값(해당 실행 객체의 상태 정보)을 꺼내와서 value에 저장
      → 그다음에 ExecutionContext 의 내용을 JobExecution / StepExecution 에 저장
      → DB에 저장 (BATCH_JOB_EXECUTION_CONTEXT / BATCH_STEP_EXECUTION_CONTEXT)
    - 공유 범위
        - Step : 각 Step의 StepExecution에 저장되며, **Step간 서로 공유 가능**
        - Job : 각 Job의 JobExecution 에 저장되며, **Job간 서로 공유 불가**
            - Job1안에 Job2이 있을 수 있음 그럴때 Step 간의 공유는 가능하므로 Step이 접근하면 된다.

<img width="812" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/7c8ed9b4-c748-4710-b43c-a081ab518535">

- ExecutionContext 에 값을 넣은게 없기때문에 안나옴

<img width="799" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/20757c67-dbb8-4d90-9ffe-51c0cba7e3b4">



# ExecutionContext 의 값을 공유함을 볼 수 있는 예제

```jsx
public class ExecutionContext3 implements Tasklet {
    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        // 실패한 데이터를 다시 활용할 수 있을 지 확인
        System.out.println("=======execution context 3 ============");
        Object name = chunkContext.getStepContext().getStepExecution().getJobExecution().getExecutionContext().get("name");
        if(name == null) {
            chunkContext.getStepContext().getStepExecution().getJobExecution().getExecutionContext().put("name", "user1");
            throw new RuntimeException("step2 was failed");
        }
        return RepeatStatus.FINISHED;
    }
}
```

![1](https://github.com/gilyeon00/TIL/assets/52391627/c1c277bf-5adf-4f91-ae52-984cbb073c8a)

- ExecutionContext3 에서, ExecutionContext의 name 키의 값을 가져올때, 없으면 값을 넣어주고 예외처리를 해준다.
- 이는 다음 재 실행때, ExecutionContext3 에 이어 실행되는 ExecutionContext4에서 ExecutionContext에 저장한 name 값을 가져와서 사용할 수 있는 지에 대한 예제이다.

```jsx
public class ExecutionContext4 implements Tasklet {
    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        System.out.println("=======execution context 4 ============");
        System.out.println("name :  " + chunkContext.getStepContext().getStepExecution().getJobExecution().getExecutionContext().get("name"));

        return RepeatStatus.FINISHED;
    }
}
```

![2](https://github.com/gilyeon00/TIL/assets/52391627/861bfdde-7fad-4267-8774-08d28833dc51)

- 예상과 같이 재시작시, ExecutionContext3부터 시작되며 ExecutionContext4에서 공유된 값을 가져올 수 있음을 볼 수 있다.
    ![3](https://github.com/gilyeon00/TIL/assets/52391627/cbe1cfd1-c50e-4436-836e-b02f28bf570f)