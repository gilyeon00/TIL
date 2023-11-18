# ItemStream

- ItemReader 와 ItemWriter 처리 과정 중 상태를 저장하고, 오류가 발생하면 해당 상태를 참조하여 실패한 곳에서 재시작 하도록 지원
- 리소스를 열고 닫아야하며, 입출력 장치 초기화 등의 작업을 해야하는 경우
- ExecutionContext 를 매개변수로 받아서 상태 정보를 업데이트 한다
- ItemReader 및 ItemWriter 는 ItemStream 을 구현해야한다.

- ItemReafder 와 ItemProcessor ,ItemWriter 를 실행시키는 시점에, ItemStream 의 메서드를 호출한다.
    - open()
        - read, write 메서드 호출전에 파일이나 커넥션이 필요한 리소스에 접근하도록 하는 초기화 작업
        - ItemReader 호출 전, 한 번만 수행
    - update()
        - 현재까지 진행된 모든 상태를 저장
        - 매번 수행한다 chunk size 만큼 데이터를 불러들여온 다음, update 메서드 호출
    - close()
        - 열려있는 모든 리소스를 안전하게 해제하고 닫음