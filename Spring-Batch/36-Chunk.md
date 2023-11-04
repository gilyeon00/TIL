# Chunk

- 여러개의 아이템을 묶은 하나의 덩어리 / 블록을 의미한다
- **한 번에 하나씩 아이템을 입력 받아 Chunk 단위의 덩어리로 만든** 후, Chunk 단위로 트랜잭션 처리를 한다.
    - 즉 Chunk 단위의 Commit 과 Rollback 이 이뤄진다,
    - 입력기(ItemReader)로 부터 하나씩 입력을 받아서 Chunk에 넣는다.
    - 출력은 Chunk 그 자체를 출력기(ItemProcessor)에 넣는다
- 일반적으로 대용량 데이터를 한 번에 처리하는 것이 아닌, 청크 단위로 쪼개어서 더 이상 처리할 데이터가 없을 때까지 반복해서 입출력하는데 사용된다.

### Chunk<I> vs Chunk<O>

- Chunk<I> 는 ItemReader 로부터 읽은 하나의 아이템을 Chunk 에서 정한 개수만큼 반복해서 저장하는 타입
- Chunk<O> 는 ItemReader 로부터 전달받은 Chunk<I> 를 참조해서 ItemProcessor 에서 적절하게 가공, 필터링한 다음, ItemWriter 에 전달하는 타입

![스크린샷 2023-11-07 오전 10.58.28.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/758b6b20-3bb2-4d91-9fad-9013bbd9fb28/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-07_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.58.28.png)

- 다음 그림의 전체 단계가 한 트랜잭션 내에서 처리된다.
- Chunk 는 **List<Item> 에 Chunk 개수만큼 item 을 저장**한다.
- ItemReader 는 item 하나씩 넣는다
- ItemWriter 는 DB 에 Chunk size 만큼의 items 를 넣는다.