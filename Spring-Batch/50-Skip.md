# Skip

- Skip 은 데이터를 처리하는 동안 설정된 Exception이 발생했을 경우,  해당 데이터 처리를 건너뛰는 기능이다.
- 데이터의 사소한 오류에 대해 Step 의 실패 처리대신 Skip 처리를 함으로써, 배치 수행의 빈번한 실패를 줄일 수 있게 한다.
- ItemReader 는 예외가 발생하면 해당 아이템만 Skip 하고 계속 진행한다.
- ItemProcessor 와 ItemWriter 는 예외가 발생하면, Chunk 의 처음으로 돌아가 Skip 된 아이템을 제외한 나머지 아이템들을 가지고 처리하게된다.


<img width="984" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/55d3a7bf-3495-46af-8102-466c7306cca0">
