# SimpleJob 아키텍처

![스크린샷 2023-09-27 오전 10.29.57.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/dc871ffd-617d-4e11-8c5a-053140ed1379/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-27_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.29.57.png)

## JobLauncher

- 역할 : SimpleJob 을 실행시킨다.

## JobParameters

- Job 을 구분하는 JobParameter 들의 집합
- JobParameters 로 Job 을 구분 할 수 있으며, JobLauncher 는 JobParameters 와 Job으로 SimpleJob 을 실행시킨다.

## JobInstance

- JobLauncher 가 Job 을 실행시키면, JobInstance 가 생성된다.

## JobExecution

- Job 이 한 번 실행이 될 때 생성되는 객체

## SimpleJob

- Step 들을 실행시킨다.
- Step 들을 실행하기 전후에 각각 JobExecutionListener 를 통해 JobListener 객체를 생성 할 수 있다. (beforeJob(), afterJob())