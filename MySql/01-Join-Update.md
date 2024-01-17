## 🙅🏻‍♀️ MySQL 은 Update 에서 서브쿼리 사용이 불가하다

```sql
UPDATE (
SELECT [a.id](http://a.id/), a.memberId, a.memberAdvisorApplyId, [a.name](http://a.name/), [b.id](http://b.id/), b.memberId, b.statusDivision
FROM memberAdvisorInfo as a
JOIN memberAdvisorApply as b
WHERE a.memberId = b.memberId
)
SET a.memberAdvisorApplyId = [b.id](http://b.id/);
```

## 🙆🏻‍♀️ Join 을 직접해줘야한다 !

```sql
UPDATE memberAdvisorInfo AS a
JOIN memberAdvisorApply AS b
ON a.memberId = b.memberId
SET a.memberAdvisorApplyId = [b.id](http://b.id/);
```

## 조건부 (Case when ~ then ~)

```sql
UPDATE memberNotificationSetting as a
JOIN memberAgreement as b
ON a.memberId = b.memberId
SET a.memberAgreementId = CASE 
	WHEN b.isAgree = 1 THEN "Y"
    WHEN b.isAgree = 0 THEN "N"
    END
where b.agreementId = 4
```

```sql
UPDATE memberNotificationSetting as a
JOIN memberAgreement as b
ON a.memberId = b.memberId
SET a.marketingAppNotificationYn = CASE 
	WHEN b.isAgree = 1 THEN "Y"
    WHEN b.isAgree = 0 THEN "N"
    END
    , a.marketingSmsNotificationYn = CASE 
	WHEN b.isAgree = 1 THEN "Y"
    WHEN b.isAgree = 0 THEN "N"
    END
    , a.marketingEmailNotificationYn = CASE 
	WHEN b.isAgree = 1 THEN "Y"
    WHEN b.isAgree = 0 THEN "N"
    END
where b.agreementId = 4
```