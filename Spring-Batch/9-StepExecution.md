# StepExecution

- Step 에 대한 한 번의 시도를 의미하는 객체로서, Step 실행 중에 발생한 정보들을 저장하고 있는 객체
    - 시작시간, 종료시간, 상태(시작됨, 완료, 실패),commit count(실행 중에 커밋된 트랜잭션의 수), rollabck count(트랜잭션 중 롤백된 횟수) 등의 속성을 가짐
- Step 이 매번 시도될 때마다 생성되며, 각 Step 별로 생성된다.
- Job 이 재시작하더라도, 이미 성공적으로 완료된 Step은 재 실행되지않고, 실패한 Step만 실행된다.
    - Job 이 재시작 : JobExecution 생성
    - Job1 - Step1(O), Step2(O), Step3(X) 으로 구성되어있다면 기본적으로 실패한 Step인 Step3부터 실핼됨
        - 완료된 Step도 실행시키는 옵션도 있음
- 이전 단계 Step이 실패해서 현재 Step을 실행하지 못했다면, 당연히 StepExecution 을 생성하지 않는다. Step이 실제로 시작됐을 때만 StepExecution이 생성되기 때문 `(문제)`
- JobExecution 과의 관계
    - Job에서 구성된 모든 Step 의 StepExecution 이 모두 정상적으로 완료되어야 JobExecution도 정상적으로 완료된다.
    - Step의 StepExecution 중 하나라도 실패하면 JobExecution은 실패한다.

## BATCH_STEP_EXECUTION 테이블과 매핑

- JobExecution 과 StepExecution 은 1:M 관계
- 하나의 Job 에 여러개의 Step으로 구성했을 경우, 각 StepExecution은 하나의 JobExecution을 부모를 가진다.

<img width="1000" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/f3bf7a4e-7b0f-4b36-8306-f6ab5a67a913">

1. **Job :  Step1 -Step2 -Step3 을 만들어줬다**

    ```jsx
    @Bean
        public Job job () {
            return jobBuilderFactory.get("my-job")
                    .start(step1())
                    .next(step2())
                    .next(step3())
                    .build();
        }
    ```

2. **일부로 step2에 throw 를 날려줬다.**
    1. step3 가 execute 되지않음

       <img width="1214" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/a0d4af50-9445-473a-abf8-db15b8572f7d">

    2. JobExecution 의 status 가 Failed 임

       <img width="1096" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/98831bbe-3ceb-43cd-ac81-82047b82740f">

    3. Step1, 2는 StepExecution이 만들어졌지만, Step2의 Failed로 인해 Step3의 StepExecution이 생기지 않음

   <img width="1127" alt="4" src="https://github.com/gilyeon00/TIL/assets/52391627/0bd39313-c39f-409c-a165-587eef0bafad">


3. **다시 정상적으로 Step 들을 실행해줬다.**
    1. step1 은 실행되지않고, step2, step3만 실행된 모습

       <img width="1124" alt="5" src="https://github.com/gilyeon00/TIL/assets/52391627/ad53e752-4e03-4166-add9-bb278caadb2e">

    2. JobInstance 가 새로 생기지않고, 기존에 생성됐던 JobInstance가 실행됨을 알 수 있다.

       <img width="782" alt="6" src="https://github.com/gilyeon00/TIL/assets/52391627/80b5325c-3767-449d-9448-a926e5a939c4">
    
   3. 같은 JobInstance ID의 JobExecution이 생김을 알 수 있음 (정상적으로 잘실행됨 Completed)

        <img width="1070" alt="7" src="https://github.com/gilyeon00/TIL/assets/52391627/326e2fc9-185a-4be2-ac1d-57d24c6edb52">

   4. step1 이 실행되지않고(이미 completed 였기때문) step2, step3의 StepExecution이 생성됨

      <img width="1044" alt="8" src="https://github.com/gilyeon00/TIL/assets/52391627/2d9a27cb-3e4c-4778-95fb-7a2650080ea4">