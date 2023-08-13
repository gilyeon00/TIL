
# 영속성 컨텍스트
: 엔티티를 영구 저장하는 환경, 논리적인 개념,
엔티티 매니저를 통해서 영속성 컨텍스트에 접근 (= 엔티티 매니저안에 영속성 컨텍스트가 있음)

## Entity Manager Factory, Entity Manager

![11](https://github.com/gilyeon00/TIL/assets/52391627/9eb2fa8a-70bd-46e6-91e1-5592df417f2b)

1. 사용자의 요청이 있을 때 마다, Entity Manager 가 생성됨
2. 이 Entity Manager 는 내부적으로 Database Connection을 사용해서 DB를 사용함

## 엔티티의 생명주기

1. 비영속 : 영속성 컨텍스트와 전혀 관계가 없는 상태 (JPA랑 전혀 관계없음)

   ![22](https://github.com/gilyeon00/TIL/assets/52391627/45ca8282-ba07-4375-80a5-5c5ae32b911e)

2. 영속 : 영속성 컨텍스트에 관리되는 상태

   ![33](https://github.com/gilyeon00/TIL/assets/52391627/b880a946-18f3-4f05-8db9-bc7e9a14f309)

   em.persist(member) 는 정확히 DB에 저장하는게 아님, 영속성 컨텍스트에 저장

   트랜잭션 커밋하는 순간 SQL문 날라감 (DB에 저장)

3. 준영속 : 영속성 컨텍스트에 저장되었다가 분리된 상태
4. 삭제