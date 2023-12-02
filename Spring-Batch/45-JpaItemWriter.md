# JpaItemWriter

- JPA Entity 기반으로 데이터를 처리하며, EntityManagerFactory 를 주입받아 사용한다
- Entity 를 하나씩 chunk 크기만큼 insert , 혹은 merge 한 다음 flush 한다.
- ItemReader 나 ItemProcessor 로 부터 아이템을 전달 받을 때는 Entity 클래스 타입으로 받아야한다.

<img width="782" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/8984ecc1-fc2d-444c-85dd-a96dea08f48e">
