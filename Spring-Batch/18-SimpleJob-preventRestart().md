# SimpleJob-preventRestart()

- Job 의 재시작 여부를 설정
- 기본값은 true 이며, false 로 설정 시, “이 Job은 재시작을 지원하지 않는다” 라는 의미
- Job 이 실패해도 재시작이 안됨
    - Job을 재시작하려하면 JobRestartException이 발생
- 재시작과 관련있는 기능으로 Job을 처음부터 실행하는 것 / 하던대로 실행하던 것과는 무관함

# preventRestart() 시나리오

[ 배경 ] Job 을 실행하려한다.

1. **JobExecution 이 있는지 확인**한다.
    1. 한 번이라도 실행한 이력이 있는지 확인
2. JobExecution 이 있다면?
    1. **preventRestart 의 값이 true / false 인지 확인**한다
        1. preventRestart 의 값이 true 라면?
            1. 3-a 부터 실행
        2. preventRestart 의 값이 false 라면?
            1. `JobRestartException` 에러 발생
3. JobExecution 이 없다면?
    1. JobInstance 생성
    2. JobExecution 생성
    3. 비즈니스 로직 실행