# FaultTolerant (내결함성)

- Spring Batch 는 Job 실행 중에 오류가 발생할 경우, 장애를 처리하기 위한 기능을 제공하며, 이를 통해 복원력을 향상시킬 수있다.
- 오류가 발생해도 Step 이 즉시 종료되기 않고, Retry / Skip 기능을 활성화함으로써 내결함성 서비스가 가능하도록 한다.
- FaultTolerant 구조는 청크 기반의 프로세스 기반 위에 Skip 과 Retry 기능이 추가되어 재정의 되어있다.

## Skip

- ItemReader / ItemProcessor / ItemWriter 에 적용할 수 있다.

## Retry

- ItemProcessor / ItemWriter 에 적용할 수 있다.

<img width="1117" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/422496e4-9350-43fb-bc60-0ffe9b81d220">

<img width="1171" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/fcc43833-38e4-462b-b2a2-8463cce043a3">
