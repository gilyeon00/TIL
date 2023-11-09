# ChunkProvider

- ItemReader 를 사용해서 소스(DB/file/Queue)로부터 **아이템을 Chunk size 만큼 읽어서 Chunk 단위로 만들어 제공**하는 도메인 객체
- **Chunk<I> (Chunk input 용 객체) 를 만들고** 내부적으로 반복문을 사용해서 ItemReader.read() 를 계속 호출하면서 item을 하나씩 Chunk에 쌓는다
- 외부로부터 ChunkProvider 가 호출될 때마다 항상 새로운 Chunk 객체가 생성된다.
- 반복문 종료 시점
    - Chunk size 만큼 item 을 읽으면 반복문은 종료되고, ChunkProcessor 로 넘어간다
    - ItemReader 가 읽은 item 이 null 일 경우, 반복문 종료 및 해당 Step 반복문까지 종료
- 기본 구현체로서 Simpl**e**ChunkProvider 와 FaultTolerantChunkProvider 가 있다.
    - FaultTolerantChunkProvider : item 읽을때 오류가 생기면 Skip 하거나 재시도를 하게끔하는 객체