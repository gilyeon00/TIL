# JobExecution

- JobInstance 에 대한 한 번의 시도를 의미하는 객체로서, Job 실행 중에 발생한 정보들을 저장하고 있는 객체
    - 시작시간, 종료시간, 상태(시작됨, 완료,  실패), 종료상태의 속성을 가짐

# JobInstance와의 관계

- JobExecution은 FAILED 또는 COMPLETED 등의 Job실행 결과 상태를 가짐
- JobExecution 의 실행 상태가 COMPLETED 면 JobInstance 실행이 완료된 것으로 간주해서 재실행 불가
- JobExecution 의 실행 상태가 FAILED 면 JobInstance 실행이 완료되지않은 것으로 간주해서 재실행 가능
    - JobParameter 가 동일한 값으로 Job을 실행할지라도 실패한 JobInstance를 계속 실행 가능
- JobExecution 의 실행 상태 결과가 COMPLETED 될 때까지 JobInstance 내에서 여러번의 시도가 생길 수 있음

## BATCH_JOB_EXECUTION 테이블과 매핑

- JobInstance 와 JobExecution 는 1:M 의 관계로서 JobInstance 에 대한 성공/실패 내역을 가짐

  <img width="1264" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/8f08d919-b85d-48c9-8f4e-53786d27b0ea">

  (마지막 row 는 디버깅 중일때 캡쳐한 상태)


# JobExecution 의 흐름도

1. JobLauncher 가 Job 과 JobParameters 를 가지고 Job을 실행시킴
2. JobRepository 를 통해 DB 에 JobInstance 가 있는 지 조회함
    1. JobInstance 가 있다면
        1. 기존에 생성했던 JobInstance 를 리턴
            1. BatchStatus 가 COMPLETED 라면
                1. 해당 JobInstance 가 이미 완료됐으므로, 더이상 실행 불가
            2. BatchStatus 가 FAILED 라면
                1. 해당 JobInstance 가 실패했으므로, 재실행 가능
                2. new JobExecution 을 생성
    2. JobInstance 가 없다면
        1. new JobInstance 를 생성
        2. new JobExecution 을 생성

<aside>
💡 JobInstance 가 실행되면 JobExecution은 항상 새로 생성된다 !

</aside>