# JdbcBatchItemWriter

- JdbcCursorItemReader 설정과 마찬가지로 datasource 를 지정하고, SQL 속성에 실행할 쿼리를 설정한다.
- JDBC 의 Batch 기능을 사용하여, bulk insert / update / delete 방식으로 처리한다
- 단건 처리가 아닌 일괄 처리이기때문에 성능에 이점을 가진다.

![1](https://github.com/gilyeon00/TIL/assets/52391627/c7268ebc-b20c-4432-9332-67c39f699edb)
