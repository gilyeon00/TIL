# SimpleJob 아키텍처

![4](https://github.com/gilyeon00/TIL/assets/52391627/a4020caa-776e-4ffa-bd2d-6d6f3ab31a82)

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