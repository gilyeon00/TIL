## JPQL
```java
@Test
public void startJPQL() {
    String qString = "select m from Member m where m.username = :username";
    Member findMember = em.createQuery(qString, Member.class)
            .setParameter("username", "member1")
            .getSingleResult();

    assertThat(findMember.getUsername()).isEqualTo("member1");
}
```

## Querydsl
```java
@Test
public void startQuerydsl() {
    JPAQueryFactory jpaQueryFactory = new JPAQueryFactory(em);
    QMember m = new QMember("m");

    Member findMember = jpaQueryFactory.select(m)
            .from(m)
            .where(m.username.eq("member1"))
            .fetchOne();

    assertThat(findMember.getUsername()).isEqualTo("member1");
}
```

파라미터 바인딩을 따로 안해주고 그냥 equal() 로 해줘도 된다

< 자동으로 sql 에 있는 jdbc의 prepared statement 로 자동 바인딩되기때문

만약 문자 더하기,, 식으로 하면 SQL 인젝션 공격 받을 수도 있고한데 querydsl은 기본적으로 자동으로 다 prepared statement 자동 바인딩 방식을 사용한다.

JPQL 은 문법이 틀리거나, m.usernome 과 같은 실수를 하게 되면 런타임 에러가 발생한다. (사용자가 해당 메서드를 실행할때 오류난걸 알게됨)

반면 Querydsl 은 컴파일시 다 검사를 하므로, 컴파일 에러가 나지 런타임에러는 날 수가 없어 안정적이다. (Q클래스 덕분)

### JPAQueryFactory 인스턴스를 생성하는 것은 필드로 뺄 수있다.

**➡️  동시성 문제가 생기지 않나?? `왜???`**
(동시에 여러 멀티 스레드에서 jpaQueryFactory에 접근해서 EntityManager 에 접근 시 동시성 문제가 생기지 않을까?)

⬅️ 🅰️ 문제없이 설계되어있고 Spring framework 가 주입해주는 EntityMagnager 자체가 멀티스레드에 아무 문제없이 설계되어있다.
여러 멀티스레드에서 들어와도 현재 내 트랜잭션이 어디에 걸려있는지에 따라 트랜잭션에 바이온딩(?) 되도록 분배해준다. 그래서 멀티스레드 환경에서도 동시성 문제없이 사용할 수 있다.
따라서 필드로 빼서 쓰는 것을 권장한다. (feat. 김영한님)

```java
@SpringBootTest
@Transactional
public class QuerydslBasicTest {

    @Autowired
    EntityManager em;

    **JPAQueryFactory jpaQueryFactory;**

    @BeforeEach     // 각 테스트 실행 전에 데이터 세팅
    public void before() {
        jpaQueryFactory = new JPAQueryFactory(em);
        Team teamA = new Team("teamA");
        Team teamB = new Team("teamB");
        em.persist(teamA);
        em.persist(teamB);

        Member member1 = new Member("member1", 10, teamA);
        Member member2 = new Member("member2", 20, teamA);

        Member member3 = new Member("member3", 30, teamB);
        Member member4 = new Member("member4", 40, teamB);
        em.persist(member1);
        em.persist(member2);
        em.persist(member3);
        em.persist(member4);
    }

    @Test
    public void startQuerydsl() {
//        JPAQueryFactory jpaQueryFactory = new JPAQueryFactory(em);
        QMember m = new QMember("m");

        Member findMember = jpaQueryFactory.select(m)
                .from(m)
                .where(m.username.eq("member1"))    // 파라미터 바인딩 처리
                .fetchOne();

        assertThat(findMember.getUsername()).isEqualTo("member1");
    }
}

```