# Repeat

: 스프링 배치 반복 및 오류 제어

- Spring Batch 는 얼마나 작업을 반복해야하는지 알려줄 수 있는 기능 제공
- 특정 조건이 충족될 때까지 (또는 특정 조건이 아직 충족되지 않을 때까지) Job, Step 을 반복하도록 배치 어플리케이션을 구성할 수 있다.
- Spring Batch 에서는 Step 의 반복과 Chunk 의 반복을 RepeatOperation 을 사용해서 처리하고 있다.
- 기본 구현체 : `RepeatTemplate`

<img width="833" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/be3128bc-cf8b-45e6-ad15-60200fa9a97c">
