# Chunk

- 여러개의 아이템을 묶은 하나의 덩어리 / 블록을 의미한다
- **한 번에 하나씩 아이템을 입력 받아 Chunk 단위의 덩어리로 만든** 후, Chunk 단위로 트랜잭션 처리를 한다.
    - 즉 Chunk 단위의 Commit 과 Rollback 이 이뤄진다,
    - 입력기(ItemReader)로 부터 하나씩 입력을 받아서 Chunk에 넣는다.
    - 출력은 Chunk 그 자체를 출력기(ItemProcessor)에 넣는다
- 일반적으로 대용량 데이터를 한 번에 처리하는 것이 아닌, 청크 단위로 쪼개어서 더 이상 처리할 데이터가 없을 때까지 반복해서 입출력하는데 사용된다.

## Chunk<I> vs Chunk<O>

- Chunk<I> 는 ItemReader 로부터 읽은 하나의 아이템을 Chunk 에서 정한 개수만큼 반복해서 저장하는 타입
- Chunk<O> 는 ItemReader 로부터 전달받은 Chunk<I> 를 참조해서 ItemProcessor 에서 적절하게 가공, 필터링한 다음, ItemWriter 에 전달하는 타입

![1](https://github.com/finaple/finaple.app.api/assets/52391627/6329c956-a81b-4cda-93f9-1e296913362d)


- 다음 그림의 전체 단계가 한 트랜잭션 내에서 처리된다.
- Chunk 는 **List<Item> 에 Chunk 개수만큼 item 을 저장**한다.
- ItemReader 는 item 하나씩 넣는다
- ItemWriter 는 DB 에 Chunk size 만큼의 items 를 넣는다.

## Chunk 를 사용하는 기본 구조

```java
	 @Bean
    public Step step2 () {
        return stepBuilderFactory.get("step2")
                .chunk(5)
                .reader()
                .processor()
                .writer()
                .build();
    }
```

## Chunk 사용하는 방법

```java
    @Bean
    public Step step2 () {
        return stepBuilderFactory.get("step2")
                .<String, String>chunk(5)
                .reader(new ListItemReader<>(Arrays.asList("item1", "item2", "item3", "item4")))
                .processor(new ItemProcessor<String, String>() {
                    @Override
                    public String process(String inputItem) throws Exception {
                        Thread.sleep(300);
                        System.out.println("item = " + inputItem);
                        return "my " + inputItem;
                    }
                })
                .writer(new ItemWriter<String>() {
                    @Override
                    public void write(List<? extends String> outputItems) throws Exception {
                        Thread.sleep(300);
                        System.out.println("items = " + outputItems);
                    }
                })
                .build();
    }
```