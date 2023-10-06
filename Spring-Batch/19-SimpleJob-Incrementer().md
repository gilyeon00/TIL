# SimpleJob-incrementer()

- JobParameters 에서 필요한 값을 증가시켜 다음에 사용될 JobParameters 객체를 반환
- 기존의 JobParameter 변경없이 Job 을 여러 번 시작하고자 할때
- JobParametersIncrementer 인터페이스를 구현한 RunIdIncremeter 구현체 지원

  <img width="644" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/1bf7c2e5-c0c0-4496-aff6-e8de187702b9">


  <img width="809" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/e6b741d1-3b23-4e25-b0a7-a788ac7a3e9a">


## JobParametersIncrementer 커스텀 구현체

```java
public class CustomJobParametersIncrementer implements JobParametersIncrementer {

    static final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyyMMdd-hhmmss");
    @Override
    public JobParameters getNext(JobParameters parameters) {
        String id = dateFormat.format(new Date());
        String name = "jobjob";
        return new JobParametersBuilder()
                .addString("run.id", id)
                .addString("name", name)
                .toJobParameters();

    }
}
```

😲 JobParameterIncremeter 를 사용할 경우, 다른 Job 과 구분할 수 있는 key 가 없으니 고정된 JobParameter 값도 JobParameters 에 넣어주는게 좋다 !

<img width="557" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/187c3190-a3d6-46c2-8517-8996c5d4df3a">
