# SimpleJob-incrementer()

- JobParameters 에서 필요한 값을 증가시켜 다음에 사용될 JobParameters 객체를 반환
- 기존의 JobParameter 변경없이 Job 을 여러 번 시작하고자 할때
- JobParametersIncrementer 인터페이스를 구현한 RunIdIncremeter 구현체 지원

  ![스크린샷 2023-09-26 오전 10.19.00.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/6f9d203a-ed81-415a-a5e6-6889191bef16/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-26_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.19.00.png)

  ![스크린샷 2023-09-26 오전 10.12.48.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/efc8a1fd-e88b-4da1-bcdb-7c3ed20aa2ac/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-26_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.12.48.png)


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

![스크린샷 2023-09-26 오전 10.37.30.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/620a6d8c-eeac-4c90-b691-23b89fd6e153/c068c19c-75a7-4053-976f-24302e561e8e/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-26_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.37.30.png)