# JdbcBatchItemWriter

- JdbcCursorItemReader 설정과 마찬가지로 datasource 를 지정하고, SQL 속성에 실행할 쿼리를 설정한다.
- JDBC 의 Batch 기능을 사용하여, bulk insert / update / delete 방식으로 처리한다
    - 하나의 데이터를 각각 DB connection 하고,  Transaction 처리하는 등 많은 연산 작업을 하게 되면, 성능에 많은 무리가 온다. 그래서 각각의 IO 처리가 아닌, bulk 에 담아두고 일괄적으로 처리하는 방식을 택한다. ➡️ 대용량 처리시의 성능 이점
- 단건 처리가 아닌 일괄 처리이기때문에 성능에 이점을 가진다.

<img width="908" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/ac127dc0-2107-455f-8c2e-70e9ce562173">
