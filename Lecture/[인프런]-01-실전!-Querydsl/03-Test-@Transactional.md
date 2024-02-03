Test Class 에 @Transactional 이 있을 경우, 롤백된다.

⬅️ DB 에 저장하는 구문이 있어도 저장되지 않는다.

```java
@SpringBootTest
@Transactional
class QuerydslApplicationTests {

	@Autowired
	EntityManager em;

	@Test
	void contextLoads() {
		Hello hello = new Hello();
		em.persist(hello);

		JPAQueryFactory query = new JPAQueryFactory(em);
//		QHello qHello = new QHello("h");
		QHello qHello = QHello.hello;  // static

		Hello result = query
				.selectFrom(qHello)
				.fetchOne();

		assertThat(result).isEqualTo(hello);
		assertThat(result.getId()).isEqualTo(hello.getId());
	}

}
```

### 왜 ?

테스트 클래스는 메모리에서 돌아가는 건가?

### 해결방법

@Commit 어노테이션 추가로 붙여주기

```java
@SpringBootTest
@Transactional
@Commit
class QuerydslApplicationTests {

	@Autowired
	EntityManager em;

	@Test
	void contextLoads() {
		Hello hello = new Hello();
		em.persist(hello);

		JPAQueryFactory query = new JPAQueryFactory(em);
//		QHello qHello = new QHello("h");
		QHello qHello = QHello.hello;  // static

		Hello result = query
				.selectFrom(qHello)
				.fetchOne();

		assertThat(result).isEqualTo(hello);
		assertThat(result.getId()).isEqualTo(hello.getId());
	}

}
```

하지만 테스트 클래스는 테스트일뿐, 실제 저장이 되지않도록 해야한다.
➡️@Commit 어노테이션을 빼는 게 좋다.