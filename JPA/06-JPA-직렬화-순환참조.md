
## 순환 참조

JPA에서 양방향으로 연결된 엔티티를 JSON 형태로 직렬화하는 과정에서, 서로의 정보를 계속 순환하며 참조하여 StackOverflowError 를 발생시키는 현상


## 직렬화

객체/데이터를 바이트 형태로 변환하여 네트워크를 통해 송수할 수 있도록 만드는 것


## 문제 상황
매핑된 데이터를 FetchType.LAZY 로 사용하고 있고,
두 Entity 가 1:N, N:1 양방향 관계를 가지고 있고,
Entity 자체를 JSON 으로 직렬화하여 

## 해결 방안
1. Entity 대신 DTO 로 반환
2. @JsonManagedReference & @JsonBackReference
양방향 관계에서 직렬화 방향을 설정하여 순환 참조를 해결할 수 있도록 설계된 어노테이션

### @JsonManagedReference
연관관계 주인 반대 Entity 에 선언
정상적으로 직렬화 수행