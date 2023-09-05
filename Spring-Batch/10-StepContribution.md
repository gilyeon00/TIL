# StepContribution

- 청크 기반의 Step 이 실행될때 만들어지는 객체로서, **StepExecution 상태를 업데이트**하는 도메인 객체
- 청크 프로세스의 변경 사항을 담고있다
- 청크 커밋 직전에 StepExecution의 apply 메서드를 호출해서 상태를 업데이트함
- ExitStatus 의 기본 종료 코드 외, 사용자 정의 종료코드를 생성해서 적용할 수 있음

<img width="1266" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/1d71020e-ca1c-4ea7-93d3-233d57d19163">

StepContribution은 Chunk 기반의 Step이 실행될때(StepExecution) Chunk 기반의 작업에서 수행되는 정보를 담고있다.

<img width="1356" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/e785bd9d-bdba-42af-a415-0d5f5c652445">
