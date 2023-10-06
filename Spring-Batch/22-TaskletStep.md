# TaskletStep

- 스프링 배치에서 제공하는 **Step 의 구현체로서, Tasklet 을 실행시키는 도메인 객체**
- RepeatTemplate(반복하는 기능을 가진 클래스) 를 사용해서 Tasklet 의 구문을 트랜잭션 경계 내에서 반복해서 실행함
    - TaskletStep 은 Tasklet 을 RepeatTemplate 을 이용해 반복해서 실행한다(기본값)
        - RepeatStatus 를 통해 설정가능 (끝낼지 FINISHED, 계속할지 CONTINUE)
    - 내부적으로 트랜잭션이 걸려있기 때문에, 개발자가 트랜잭션을 설정해주지 않아도됨
        - 하나의 Tasklet  = 하나의 트랜잭션
- Task 기반과 Chunk 기반으로 나눠서 Tasklet 을 실행함
    - TaskletStep 은 Tasklet 구현체를 이용해서 Task 기반 작업 수행
    - TaskletStep 은 ChunkOrientedTasklet 구현체를 이용해서 Chunk 기반 작업 수행

## **Task 기반**

- Step 의 실행단위
- 단일 작업 기반으로 처리되는 것이 더 효율적인 경우 사용
- 주로 Tasklet 구현체를 만들어서 사용
- 대량 처리를 하는 경우 Chunk 기반에 비해 더 복잡한 구현 필요

## **Chunk 기반**

- Step 의 실행단위
- 하나의 큰 덩어리를 n개씩 나눠서 실행한다는 의미로, 대량 처리를 하는 경우 효과적으로 설계됨
- `ItemReader`, `ItemProcessor`, `ItemWriter` 를 사용하며 Chunk 기반 전용 Tasklet 인 ChunkOrientedTasklet 구현체가 제공됨

<img width="971" alt="5" src="https://github.com/gilyeon00/TIL/assets/52391627/1b5c3b81-a9ee-46f6-b67d-514de39d5f54">

![6](https://github.com/gilyeon00/TIL/assets/52391627/cd5028c9-d6b2-4d95-ae65-39f1d86ed2cc)
![7](https://github.com/gilyeon00/TIL/assets/52391627/84049281-8637-46e5-9366-2f3ee3503c96)
