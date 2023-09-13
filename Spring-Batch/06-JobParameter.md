# JobParameter
- Job을 실행할 때 함께 포함되어 사용되는 파라미터를 가진 도메인 객체
- 하나의 Job에 존재할 수 있는 여러개의 JobInstance를 구분하기 위한 용도
- JobParmeters와 JobInstance 는 1:1 관계

# JobParameter 생성 및 바인딩

- 어플리케이션 실행 시 주입
    - Java -jar spring-batch-example-0.0.1-SNAPSHOT.jar date=20210204
- 코드로 생성
    - JobParmeterBuilder
    - DefaultJobParametersConverter
- Spring Expression Language(SpEL) 이용
    - SpEL 표현식 : #{표현식}
    - 속성 값 참조 : ${property.name}
        - EX. #{ ${firstProperty} + 30 }
    - @Value(”#{jobParameter[requestDate]}”)
        - @JobScope, @StepScope 선언 필수

## JobParameters 와 JobParameter의 구성

<img width="1375" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/90a82a37-b51e-49d2-8a2d-3ef468e65892">

<img width="923" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/34cfc736-5d08-435d-8919-8857e24ed898">

`JobParameters`는
LinkedHashedMap<String, JobParmeter> parameters 로 구현되어있음 → JobParameter 클래스가 필요함

<img width="574" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/a8c2f87d-28e6-46c9-9f47-e43c2ce4e31b">

<img width="402" alt="4" src="https://github.com/gilyeon00/TIL/assets/52391627/4c57bec4-b03f-4e6a-ae56-b40ad0704aaf">

`JobParameter` 는 ParameterType enum을 가지고 있는데, 이의 종류는 String, Date, Long, Double 이다.

<img width="605" alt="5" src="https://github.com/gilyeon00/TIL/assets/52391627/0682eca9-8e5a-4fab-9d9a-2a69f185ecf9">

JobParametersBuilder 를 통해 JobParameters 를 생성 할 수 있다.
.add~ 와 같은 각각의 라인은 JobParameter이다.

하지만 이는 JobParameters로 묶여지기 때문에, 한 필드라도 다르면 다른 파라미터로 간주한다.

- Job을 실행했을 때, 생성된 parameters

<img width="1013" alt="6" src="https://github.com/gilyeon00/TIL/assets/52391627/755c421d-4bc0-49bf-8030-c2c1484df76c">

- 똑같은 Job을 실행했을 때, 다른 parameter가 같아도 Date가 다르기때문에 JobInstance가 생성됨

<img width="949" alt="7" src="https://github.com/gilyeon00/TIL/assets/52391627/c117b20e-97ac-47e9-b208-3603d3f923c6">


### 🧐 이렇게 저장한 JobParameters 의 값은 어디서, 어떻게 참조할까?

⇒ Step 단계에서 JobParameters 의 값을 참조한다.

- `StepContribution`

StepContribution → StepExecution → JobExecution → JobParameters

JobParameters 로 반환됨      ← 우리가 얻고자 하는 결과

<img width="946" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/ae16c183-a295-4b53-bfb4-97e5065f67f0">

- `ChunkContext`

ChunkContext → StepContext → StepExecution → JobExecution → JobParameters

Map 으로 반환됨             ← JobParameters 로 반환되진 않기에, 나누자면 값만 확인하는 용도

<img width="687" alt="2" src="https://github.com/gilyeon00/TIL/assets/52391627/be39c090-5d0b-46e3-b2fc-baef0a623370">

<img width="422" alt="3" src="https://github.com/gilyeon00/TIL/assets/52391627/9aa1df93-eca8-43c5-a9dd-fcd9e7a23998">