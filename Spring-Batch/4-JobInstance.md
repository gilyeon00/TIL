# JobInstance
- Job이 실행될 때 생성되는 Job의 논리적인 실행 단위 객체 로서, 고유하게 식별 가능한 작업 실행을 나타낸다
- Job의 설정과 구성은 동일하지만 Job이 실행되는 시점에 처리하는 내용은 다르기 때문에 Job의 실행을 구분해야됨
    - 하루에 2번씩 배치 Job이 실행된다면 매일 실행되는 각각의 Job을 JobInstance로 표현한다.
- JobInstance 생성 및 실행
    - 처음 시작하는 Job + JobParameter 일 경우 새로운 JobInstance 리턴
    - 이전과 동일한 Job + JobParameter 으로 실행할 경우 이미 존재하는 JobInstance 리턴
        - 내부적으로 JobName + jobKey (jobParametes 의 해시값)를 가지고 JobInstance 객체를 얻음
- Job 과는 1:M 관계