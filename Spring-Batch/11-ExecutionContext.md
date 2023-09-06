# ExecutionContext

- Job과 Step에 포함된 도메인은 아니고, 관련이 있는 도메인
- 프레임워크에서 유지 및 관리하는 key/value 으로 된 컬렌션 (일종의 map)
    - DB 에 직렬화 한 값으로 저장됨 { “key” : “value” }
- StepExecution 또는 JobExecution 객체의 상태(속성)를 저장하는 공유 객체
    - 공유 객체 : 여러 객체가 참조하는 객체
    - Job / Step 이 실행하면서, 중간 중간 리스너 등을 이용해서 ExecutionContext 객체의 key에 맞는 값(해당 실행 객체의 상태 정보)을 꺼내와서 value에 저장
      → 그다음에 ExecutionContext 의 내용을 JobExecution / StepExecution 에 저장
      → DB에 저장 (BATCH_JOB_EXECUTION_CONTEXT / BATCH_STEP_EXECUTION_CONTEXT)
    - 공유 범위
        - Step : 각 Step의 StepExecution에 저장되며, **Step간 서로 공유 가능**
        - Job : 각 Job의 JobExecution 에 저장되며, **Job간 서로 공유 불가**
- Job 재시작시 이미 처리한 Row 데이터는 건너뛰고, 이후부터 수행하도록 할 때 상태 정보를 활용한다.

<img width="812" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/7c8ed9b4-c748-4710-b43c-a081ab518535">

- ExecutionContext 에 값을 넣은게 없기때문에 안나옴

<img width="799" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/20757c67-dbb8-4d90-9ffe-51c0cba7e3b4">


<img width="875" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/299acddf-5cd3-4969-8171-1d41e8bea262">
