# SimpleJob-preventRestart()

- Job 의 재시작 여부를 설정
- 기본값은 true 이며, false 로 설정 시, “이 Job은 재시작을 지원하지 않는다” 라는 의미
- Job 이 실패해도 재시작이 안됨
    - Job을 재시작하려하면 JobRestartException이 발생
- 재시작과 관련있는 기능으로 Job을 처음부터 실행하는 것 / 하던대로 실행하던 것과는 무관함

