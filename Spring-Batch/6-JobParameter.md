# JobParameter
- Job을 실행할 때 함께 포함되어 사용되는 파라미터를 가진 도메인 객체
- 하나의 Job에 존재할 수 있는 여러개의 JobInstance를 구분하기 위한 용도
- JobParmeters와 JobInstance 는 1:1 관계

# JobParameter 생성 및 바인딩

- 어플리케이션 실행 시 주입
    - Java -jar LogBatch.job requestDate-20120824
- 코드로 생성
    - JobParmeterBuilder
    - DefaultJobParametersConverter
- SpEL 이용
    - @Value(”#{jobParameter[requestDate]}”)
        - @JobScope, @StepScope 선언 필수