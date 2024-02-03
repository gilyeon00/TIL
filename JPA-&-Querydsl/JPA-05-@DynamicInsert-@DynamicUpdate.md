
# @DynamicInsert 와 @DynamicUpdate
```java
@Getter
@Setter
@Entity
@Table(name = "insurance_talk")
public class InsuranceTalk {    
	@Id    
	@GeneratedValue(strategy = GenerationType.IDENTITY)    
	private Long id;   
 
	@OneToOne(fetch = FetchType.LAZY)    
	@JoinColumn(name = "memberId")    
	private Member member;    

	@Column    
	private String title;  
  
	@Column    
	private String content;   
 
	@Column    
	private int isPublic;    

	@Column    
	private Integer score;
}
```

### [ @DynamicInsert  설정 전 ] memberId, title만 insert 하고 싶다면?

내가 memberId, title만 insert 하고 싶어서,
memberId 와 title 만 값을 설정하고 JPA를 통해 DB에 저장하도록 하려한다.

하지만 JPA 구현체인 Hibernate 를 통해 데이터베이스로 날라간 쿼리를 보면, 의도한 memberId 와 title 칼럼뿐만 아니라, 다른 칼럼들에 대한 쿼리가 만들어진다.

(이는 수정도 마찬가지이다.)

```java
Hibernate: 
    insert 
    into
        insurance_talk
        (id, member_id, title, content, isPublic, score) 
    values
        (default, ?, ?, ?, ?, ?, ?)
```

## 그럼 왜 JPA는 불필요하게 모든 필드를 가지고 올까?

모든 필드를 가지고 와서, 업데이트를 하면 DB에 보내지는 데이터가 많아지는 단점이 있을 텐데..

### 모든 필드를 업데이트시 갖는 장점

1. 모든 필드를 사용하면 바인딩되는 데이터만 다를 뿐, 등록/수정 쿼리가 항상 같다.
    1. 내가 title, content 만 업데이트를 하고 싶어도, 기존 쿼리 재사용 가능

        ```java
        Hibernate: 
            insert 
            into
                insurance_talk
                (id, member_id, title, content, isPublic, score) 
            values
                (default, ?, ?, ?, ?, ?, ?)
        ```

    2. 따라서, 애플리케이션 로딩 시점에 쿼리를 미리 생성해두고, 재사용이 가능하다. `←어떻게?`
2. DB에 동일한 쿼리를 보내면, DB는 이전에 한 번 파싱된 쿼리를 재사용할 수 있다.

# ****@DynamicInsert & @DynamicUpdate****

위의 내용과 같이 JPA 는 기본적으로 모든 필드에 대한 작업을 실행한다.
여기서 `@DynamicInsert`& `@DynamicUpdate` 를 사용하면, 실제로 사용할 컬럼들에 대해서만 쿼리를 작성할 수 있다.

### 설정 방법

```java
@DynamicInsert
@DynamicUpdate
public class InsuranceTalk {  }
```

### [ @DynamicInsert  설정 후 * memberId, title만 insert 하고 싶다면?

```java
Hibernate: 
    insert 
    into
        insurance_talk
        (id, member_id, title) 
    values
        (default, ?, ?)
```