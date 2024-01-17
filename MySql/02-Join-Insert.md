# Join 하고 Insert 하기

- 배경
  - 기존에 Member 테이블이 존재
  - Member 테이블과 1:1 관계인 MemberNotificationSetting 이 필요
- 요구사항
  - 이때 MemberNotificationSetting 는 모든 Member 에 대해 매칭되어야한다.
  - 기존의 Member 에서, 삭제되지 않은 유저들에 한해 MemberNotificationSetting 테이블에 memberId 를 추가해줘야한다.
```sql
INSERT INTO memberNotificationSetting(memberId)
(
	SELECT b.id
	FROM memberNotificationSetting as a
	RIGHT JOIN member as b
	ON a.memberId  = b.id
	WHERE b.delYn = "N"
);
```