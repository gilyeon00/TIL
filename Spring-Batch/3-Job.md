# Job
- 배치 계층 구조에서 가장 상위에 있는 개념으로, 하나의 배치 작업 자체를 의미
    - ex) API 서버의 접속 로그 데이터를 통계 서버로 옮기는 배치 인 Job 그 자체
- Job Configuration 을 통해 생성되는 객체 단위로서 배치작업을 어떻게 구성하고, 실행할 것인지 전체적으로 설정하고 명세해 놓은 객체
- 배치 Job 을 구성하기 위한 최상위 인터페이스이며, 스프링 배치가 기본 구현체를 제공함
- 여러 Step을 포함하고 있는 컨테이너로서 반드시 한개 이상의 Step으로 구성됨

## SimpleJob

- Job의 기본 구현체 중 하나
- 순차적으로 Step을 실행시키는 Job
- 모든 Job에서 유용하게 사용할 수 있는 표준 기능을 갖고 있음
- 내부적으로 steps라는 List를 가지고 있고, 여기에 step을 add하는 형태
- step을 포함하고 있는 컨테이너 역할

## FlowJob

- Job의 기본 구현체 중 하나
- 특정한 조건과 흐름에 따라 Step을 구성하여 실행시키는 Job
- Flow 객체를 실행시켜서 작업을 진행 (steps X)

## AbstractJob

name - Job 이름

restartable - 재시작 여부 (기본값: true)

JobRepository - 메타데이터 저장소

JobExecutionListener - Job 이벤트 리스너

JobParametersIncrementer - JobParameter 증가기

JobParametersValidator -JobParameter 검증기

SimpleStepHandler - Step 실행 핸들러

## Job : 최상위 인터페이스

void execute(JobExecution) : Job 실행 메서드



# Job의 실행
1. JobLauncher

run(job, parameters) 을 통해 Job을 실행시킨다.

2. Job

execute() 를 통해 steps를 실행시킨다.

이 step은 1~N개이며,  순차적으로 실행된다.
