# ItemReader

- 다양한 입력으로부터 데이터를 읽어서 제공하는 인터페이스
  - Flat(플랫) 파일 - csv, txt (고정 위치로 정의된 데이터 필드나 특수문자오 구별된 데이터의 행)
  - XML, JSON
  - Database
  - JMS(Java Message Service), RabbitMQ, 카프카(kafka) 와 같은 Message Queuing 서비스
  - Custom Reader - 구현 시 멀티 스레드 환경에서 스레드에 안전하게 구현할 필요가 있음
- **ChunkOrientedTasklet 실행 시 필수적 요소로 설정**해야한다.

### read()

- 입력 데이터를 읽고 다음 데이터로 이동한다.
- 아이템 하나를 리턴하며, 더 이상 아이템이 없는 경우 null 리턴
- 아이템 하나는 파일의 한줄, DB의 한 row 혹은 XML 파일에서 하나의 엘리먼트가 될 수 있다.
- 더 이상 처리해야할 item 이 없어도 예외가 발생하지 않고, ItemProcessor / ItemWriter 와 같은 다음 단계로 넘어간다.
- ExecutionContext 에 read 와 관련된 여러 상태 정보를 저장해서 재시작 시 다시 참조하도록 지원한다.

![1](https://github.com/gilyeon00/TIL/assets/52391627/f435cad9-5cd9-48b1-8736-b0950e00c32b)

# ItemProcessor

- 데이터를 출력하기 전에 데이터를 가공, 변형, 필터링하는 역할
- ItemReader 및 ItemWriter 와 분리되어 **비즈니스 로직을 구현**할 수 있다.
- ItemReader 로 부터 받은 아이템을 특정 타입으로 변환해서 ItemWriter 에게 넘겨줄 수 있다.
- ItemReader 로 부터 받은 아이템들 중 **필터과정을 거쳐 원하는 아이템들만 ItemWriter 에게 넘겨**줄 수 있다.
  - ItemProcessor 에서 process() 실행결과 null을 반환하면 Chunk<O> 에 저장되지 않기 때문에 결국 ItemWriter 에 전달되지않는다.
- ChunkOrientedTasklet 실행 시 선택적 요소이기 때문에 Chunk 기반 프로세싱에서 ItemProcessor 단계가 반드시 필요한 것은 아니다.
- ItemProcessor 는 대부분 커스터마이징해서 사용되곤 한다. (제공 라이브러리도 그래서 적음)

### process()

- <I> 제네릭은 ItemReader 에서 받을 데이터 타입을 지정한다
- <O> 제네릭은 ItemWriter 에서 보낼 데이터 타입을 지정한다
- 아이템 하나씩 가공 처리하며, null을 리턴할 경우 해당 아이템은 Chunk<O> 에 저장되지 않는다.
  - ItemWriter 에게 리턴하지 않는다

# ItemWriter

- Chunk 단위로 데이터를 받아 일괄 출력 작업을 위한 인터페이스
  - Flat(플랫) 파일 - csv, txt (고정 위치로 정의된 데이터 필드나 특수문자오 구별된 데이터의 행)
  - XML, JSON
  - Database
  - JMS(Java Message Service), RabbitMQ, 카프카(kafka) 와 같은 Message Queuing 서비스
  - Custom Writer
- 아이템 리스트를 전달 받는다. ( 아이템 하나 X )
- **ChunkOrientedTasklet 실행 시 필수적 요소로 설정**해야한다.
- 보통 ItemReader 구현체와 1:1 대응 관계인 구현체들로 구성되어있다.

### write(List<? extents T> items)

- 출력 데이터를 아이템 리스트로 받아 처리한다.
- 출력이 완료되고 트랜잭션이 종료되면, 새로운 Chunk 단위 프로세스로 이동한다.

![IMG_DB5E23087416-1](https://github.com/gilyeon00/TIL/assets/52391627/541a09e6-f5d3-45bb-9225-a28d88642696)
