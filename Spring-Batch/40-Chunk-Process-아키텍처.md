# Chunk Process 아키텍처

1. Job 을 실행한다
2. TaskletStep 이 실행된다
3. TaskletStep 내부의 RepeatTemplate 이라는 반복기가 ChunkOrientedTasklet(Chunk 기반의 Tasklet) 을 반복적으로 실행시킨다.
    1. ChunkOrientedTasklet 클래스가 실행될 때 Transaction 처리가 시작된다.

### ChunkOrientedTasklet

- **SimpleChunkProvider**
    - 내부의 RepeatTemplate 이라는 반복기를 통해 Chunk size 만큼 ItemReader 가 아이템을 읽어오게 한다 ◀️ Begin Transaction
    - ItemReader 를 통해서 아이템을 읽어오도록 하는 클래스
- **SimpleChunkProcessor**
    - ItemProcessor 와 ItemWriter 를 통해서 읽어온 데이터들을 가공하고 쓰게 작업하도록 하는 클래스

### ItemReader

- 데이터를 읽어올때 null 체크 한다.
    - null 일 경우, RepeatStatus.FINISH 상태값을 통해 Step 을 종료 시킨다.