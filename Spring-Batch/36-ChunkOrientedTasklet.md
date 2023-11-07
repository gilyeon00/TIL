# ChunkOrientedTasklet

- ChunkOrientedTasklet 은 스프링 배치에서 제공하는 Tasklet 의 구현체로서, Chunk 지향 프로세싱을 담당하는 객체
- ItemReader, ItemWriter, ItemProcessor 를 사용해서 Chunk 기반의 데이터 입출력 처리를 담당
- TaskletStep 에 의해서 반복적으로 실행되며, ChunkOrientedTasklet 이 실행될 때마다 매번 새로운 Transaction 이 생성되어 처리됨
- exception 이 발생할 경우, 해당 Chunk 는 롤백되며, 이전에 커밋한 Chunk는 완료된 상태가 유지됨
  - 자체적으로 ChunkContext 에 저장해놓은(버퍼에 담아놓은) 데이터를 가져오는 방식으로 롤백한다. DB 에 검색해서 롤백하는 것이아니다.
- 내부적으로 ItemReader 를 핸들링하는 ChunkProvider 와 ItemProcessor, ItemWriter 를 핸들링하는ChunkProcessor 타입의 구현체를 가지고 있음

---

<img width="1103" alt="1" src="https://github.com/finaple/finaple.app.api/assets/52391627/1aed4d18-95a4-49ce-845d-a1d7894645ae">

1. ChunkProvider 는 `ItemReader` 에게 1건씩의 데이터를 읽게 지시한다.
    - Chunk size 만큼 반복 (더이상 읽을 데이터가 없을 때 까지)
2. ChunkOrientedTasklet 은 ItemReader 가 데이터를 Chunk size 만큼 다 가져오면, ChunkProcessor 에게 해당 데이터뭉탱이를 처리하도록 전달한다.
3. ChunkProcessor 는 `ItemProcessor` 에게 데이터를 넘겨주고, 이를 가공하게끔 한다.
4. ChunkProcessor 는 `ItemWriter` 에게 가공한 아이템들을 **List** 에 담아서 전달한다. (커밋)

위의 1~4 단계는 한 트랜잭션 내에 있다.