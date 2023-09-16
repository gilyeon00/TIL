# JobLauncherApplicationRunner

- Spring Batch Job 을 실행하는 주체
- Spring Batch 작업을 시작하는 ApplicationRunner 로서 BatchAutoConfiguration 에서 생성됨

  ![스크린샷 2023-09-20 오후 4.11.22.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/07e2bd2b-2b53-40d6-965a-afae1b86d027/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.11.22.png)

- 스프링 부트에서 제공하는 ApplicationRunner 의 구현체로 어플리케이션이 정상적으로 구동되자 마다 실행됨
- 기본적으로 **Bean**으로 등록된 **모든 job** 을 실행시킨다
    - 각각의 Bean으로 등록된 모든 Job을 Spring boot 가 스캔해서 `JobLauncherApplicationRunner` 에게 전달
      ➡️ `JobLauncherApplicationRunner` 가 전달받은 Job 개수만큼 Job 실행
- `JobLauncherApplicationRunner` 는 ApplicationRunner 를 상속받고 있다.

  ![스크린샷 2023-09-20 오후 3.32.33.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/220971b1-534a-43d2-b060-3e8f4ff2dd8b/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.32.33.png)

    - ApplicationRunner 는 인터페이스로, Spring boot 어플리케이션이 실행될때 가장 먼저 실행되게끔하는 run 메서드가 있다.

      ![스크린샷 2023-09-20 오후 3.35.38.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/e73f189a-d911-4840-bd63-822bacc29bcc/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.35.38.png)

        - 이 run 메서드의 인자는 ApplicationArguments 로, argument로 입력해놓은 job name을 실행하게끔 한다.

# BatchProperties

![스크린샷 2023-09-20 오후 4.12.41.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/2850dd92-7925-410f-bccd-259995474b57/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.12.41.png)

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

  ![스크린샷 2023-09-20 오후 4.18.54.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/72887fc9-88df-405c-9796-56eeb51c89aa/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.18.54.png)