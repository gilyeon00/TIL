### Transition

: 전환 , 전이

SimpleJob 에서는 마지막 Step 가 종료될때 성공 여부에 따라 BatchStatus 와 ExitCode 가 달라졌다.
그럼 `FlowJob` 은 **조건부로 Step/Flow 가 실행**되는데, BatchStatus 와 ExitCode 는 어떻게 정의 될까?  
그리고 이 BatchStatus 에는 어떤 종류가 있는 지 알아보자.

## BatchStatus

- JobExecution 과 StepExecution의 속성으로 Job 과 Step 의 종료 후 최종 결과 상태가 무엇인지 정의
- **SimpleJob**
  - 마지막 Step 의 BatchStatus 값을 Job 의 최종 BatchStatus 값으로 반영
  - Step 이 실패한 경우, 해당 Step 이 마지막 Step 이 된다.
- **FlowJob**
  - Flow 내 Step 의 ExitStatus 값을 `FlowExecutionStatus` 값으로 저장
  - 마지막 Flow 의 FlowExecutionStatus 값을 최종 BatchStatus 값으로 반영
- COMPLETED, STARTING, STARTED, STOPPING, STOPPED, FAILED, ABANDONED, UNKNOWN

  <img width="781" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/23060444-ee10-4739-bfc7-957c80766c71">


## ExitStatus

- JobExecution 과 StepExecution 의 속성으로 Job 과  Step 의 실행 후, 어떤 상태로 종료되었는 지 정의
- 기본적으로 ExitStatus 는 BatchStatus 와 동일한 값으로 설정된다.
- **SimpleJob**
  - 마지막 Step 의 ExitStatus 값을 Job 의 최종 ExitStatus 값으로 반영
- **FlowJob**
  - Flow 내 Step 의 ExitStatus 값을 `FlowExecutionStatus` 값으로 저장
  - 마지막 Flow 의 FlowExecutionStatus 값을 Job 의 최종 ExitStatus 값으로 반영
- UNKNOWN, EXECUTING, COMPLETED, NOOP, FAILED, STOPPED

## FlowExecutionStatus

- FlowExecution 의 속성으로 Flow 의 실행 후, 최종 결과 상태가 무엇인지 정의한다.
- Flow 내 Step 이 실행되고 나서 ExitStatus 값을 FlowExecutionStatus 값으로 저장
- FlowJob 의 배치 결과 상태에 관여함
- COMPLETED, STOPPED, FAILE, UNKNOWN