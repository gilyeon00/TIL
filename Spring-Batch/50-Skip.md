# Skip

# Skip

- Skip 은 데이터를 처리하는 동안 설정된 Exception이 발생했을 경우,  해당 데이터 처리를 건너뛰는 기능이다.
- **데이터의 사소한 오류에 대해 Step 의 실패 처리대신 Skip 처리를 함으로써, 배치 수행의 빈번한 실패를 줄일 수 있게 한다.**
- `ItemReader` 는 예외가 발생하면 해당 아이템만 Skip 하고 계속 진행한다.
- `ItemProcessor` 와 `ItemWriter` 는 예외가 발생하면, **Chunk 의 처음으로 돌아가 Skip 된 아이템을 제외한 나머지 아이템들을 가지고 처리** (다시 ItemReader 를 통해 데이터를 받고 처리)하게된다.

<img width="984" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/55d3a7bf-3495-46af-8102-466c7306cca0">

---

## 📌 Skip 기능은 내부적으로 SkipPolicy 를 통해서 구현되어있다.

- Chunk 관련 작업을 하다, 예외가 발생할 경우 SkipPolicy 가 그 예외에 대해서 Skip 할 건지 말 선지 Skip 정책을 통해서 검사한다.

### [Skip 가능 여부를 판별하는 기준](https://github.com/gilyeon00/TIL/blob/main/Spring-Batch/03-Job.md)

- Skip 대상에 포함된 예외인지 여부
- Skip counter 를 초과했는지 여부
    - 무한대로 Skip 하는게 아니라, limit, counter 를 정할 수 있다

![1](https://github.com/gilyeon00/TIL/assets/52391627/2030b3f4-e8ac-4c71-bc2a-7f10503f514d)

### SkipPolicy

- Skip 정책에 따라 **아이템의 Skip 여부를 판단하는 클래스**
- 스프링 배치가 기본적으로 제공하는 SkipPolicy 구현체들이 있으며, 필요 시 직접 생성해서 사용가능
  - 기본적으로는 `LimitCheckingItemSkipPolicy` 구현체를 사용한다.
- 내부적으로 Classfier 클래스들을 활용한다.
- 반환값이 true 이면 Skip 한다

![1](https://github.com/gilyeon00/TIL/assets/52391627/84032e0b-8021-482e-8dc4-95f1f356a465)

## 📌 사용 방법에 따른 예제

```sql
@Bean
    public Step step1() {
        return stepBuilderFactory.get("step1")
                .<String, String>chunk(5)
                .reader(new ItemReader<String>() {
                    int i = 0;

                    @Override
                    public String read() {
                        i++;
                        if (i == 3) {
                            throw new IllegalArgumentException("skip test");
                        }
                        return i > 20 ? null : String.valueOf(i);
                    }
                })
                .processor(itemProcessor())
                .writer(itemWriter())
                **.faultTolerant()
                .skip(SkippableException.class) // 예외 클래스 타입을 지정해야한다.
                .skipLimit(3)
                .build();**
    }
```

- skip() : 예외 발생 시에 Skip 할 예외 타입 설정
- skipLimit() : Skip 제한 횟수 설정 (ItemReader, ItemProcessor, ItemWriter 횟수 합)
- skipPolicy() : Skip 을 어떤 조선과 기준으로 적용 할 것인지 정책 설정
- noSkip() : 예외 발생 시 Skip 하지 않을 예외 타입 설정