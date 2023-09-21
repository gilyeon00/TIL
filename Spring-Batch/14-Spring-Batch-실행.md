# JobLauncherApplicationRunner

- Spring Batch Job 을 실행하는 주체
- Spring Batch 작업을 시작하는 ApplicationRunner 로서 BatchAutoConfiguration 에서 생성됨

  <img width="1021" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/02afe003-3519-49e0-a877-d52be01e2229">

- 스프링 부트에서 제공하는 ApplicationRunner 의 구현체로 어플리케이션이 정상적으로 구동되자 마다 실행됨
- 기본적으로 **Bean**으로 등록된 **모든 job** 을 실행시킨다
    - 각각의 Bean으로 등록된 모든 Job을 Spring boot 가 스캔해서 `JobLauncherApplicationRunner` 에게 전달
      ➡️ `JobLauncherApplicationRunner` 가 전달받은 Job 개수만큼 Job 실행
- `JobLauncherApplicationRunner` 는 ApplicationRunner 를 상속받고 있다.

  <img width="695" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/b56eda5d-08d6-41e8-b9fd-a5c34854044f">

    - ApplicationRunner 는 인터페이스로, Spring boot 어플리케이션이 실행될때 가장 먼저 실행되게끔하는 run 메서드가 있다.

      <img width="689" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/90092b20-3dc7-4702-b1ae-abbd572731fc">

        - 이 run 메서드의 인자는 ApplicationArguments 로, argument로 입력해놓은 job name을 실행하게끔 한다.

# BatchProperties

<img width="754" alt="4" src="https://github.com/gilyeon00/TIL/assets/52391627/50e75fa3-39f6-4508-b9e3-d64d1b95fcd4">

- Spring Batch 의 환경 설정 클래스 - 설정들을 초기화하는 역할
- Job 이름, 스키마 초기화 설정, 테이블 Prefix 등의 값을 설정할 수 있다.
- `application.properties` / `application.yml` 파일에 설정함
    - batch:
      job:
      names: ${job.name: NONE}
      initalize-schema: NEVER
      tablePRefix: SYSTEM

# Job 실행 옵션

- 지정된 Batch Job 만 실행하도록 할 수 있음
- spring.batch.job.names: ${job.name:NONE}
    - 물론 하드코딩도 가능하지만, ( spring.batch.job.names: helloJob )
      애플리케이션 실행할 때 argument에 job name 바인딩을 통해 실행할 수 있다
- 어플리케이션 실행 시 Program arguments 로 job 이름을 입력하면 해당 job이 실행됨
    - —job.name=my-job
    - —job.name=helloJob,simpleJob (하나 이상의 job을 실행할 셩우 쉼표로 구분해서 입력함)

    ```yaml
    spring:
      datasource:
        hikari:
          jdbc-url: jdbc:mysql://localhost:3306/spring_batch?serverTimezone=Asia/Seoul
          driver-class-name: com.mysql.cj.jdbc.Driver
          username: root
          password: 12081208
    
      batch:
        job:
          names: ${job.name:NONE}
        jdbc:
          initialize-schema: always
    ```

  <img width="1152" alt="5" src="https://github.com/gilyeon00/TIL/assets/52391627/81b7f6aa-3efe-4b98-8529-d05af81e8bf7">