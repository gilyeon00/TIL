# JpaCursorItemReader

- Spring Batch 4.3 버전부터 지원
    - 해당 버전이전에는 JDBC Cursor 방식을 지원했음
- Cursor 기반의 JPA 구현체로서, EntityManagerFactory 객체가 필요하며, 쿼리는 JPQL 을 사용한다.
- Step 은 JpaCursorItemReader 을 통해서 데이터를 읽을 수 있다.

<img width="715" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/793d4ad9-de7d-4794-a3fd-55955fa8df82">

---

## ✈️ JpaCursorItemReader 가 데이터를 읽기 전에, ItemStream 에서 하는 일 !

1. Step 은 ItemStream 의 open() 을 통해 EntityManager 를 만든다
2. EntityManager 를 생성하면, 우리가 설정한 JPQL 을 통해서 query 문을 생성하게 된다
3. JPQL 로 생성된 최종 값이 ResultList 로 반환된다
4. List 로 반환된 값은 Stream 으로 바꾼다 (ResultStream)
5. 이 ResultStream 을 통해 Iterator 로 만들어낸다.

> **Iterator 이란 ?**
>
- 사전적 의미
  - iterate : (계산, 컴퓨터 처리 절차를) 반복하다
  - iterator : 반복자
- 기능 : **ArrayList, HashSet과 같은 컬렉션** 안을 돌면서, 들어있는 데이터에 접근할 수 있게 하는 객체
- 🧐 for 문이나 while 문을 사용하면 되지않을까?
  - List 에는 index 가 있지만, Set 은 index 가 없기때문에 for-each 문 사용이 불가하다.
  - 모든 컬렉션 객체들이 표준화된 방법을 제공받아 사용할 수 있기 때문이 아닐까 싶다.

> **Collection이란  ?**
>
- 자바에서 제공하는 자료구조들의 인터페이스로 List, ArrayList, Stack, Quque, LinkedList 등이 이를 상속받고있다
- 즉, 이러한 컬렉션 인터페이스를 상속받는 클래스들에 대해 Iterator 인터페이스 사용 가능하다

<img width="690" alt="1" src="https://github.com/gilyeon00/TIL/assets/52391627/049f35d2-7f05-4ae6-a6da-69097531f048">

---

## 🎃 JpaCursorItemReader 에서 하는 일

1. ResultStream 에게 데이터를 읽어오라한다 doRead()
2. ItemStream 의 open() 를 통해 이미 받아온 ResultStream 은 내부적으로 List 를 가지고 있다.
3. 이 List 는 Stream 으로 뽑아낼 수 있다. ➡️ Iterator 의 next() 를 통해 데이터를 1개씩 끌어온다.

⭐️ 결국 가져오는건 직접 DB로 부터 가져오는 것이 아니다. 이미 연결해서 가져온 데이터가 있는 상태이다.

▶️ Iterator 로부터 데이터를 하나씩 가져와서 전달을 하므로 Streaming 방식이다 ! (이는 ChunkSize 만큼만 반복한다.) 

### ✅ JdbcCursorItemReader 와 다른점

- ResultSet 으로 next 할 때마다, DB 와 연동을 해서 DB에서 레코드를 하나씩 가져와서 object 로 변환한다.

---

### 🗑️Chunk Size 만큼 더이상 불러올 데이터가 없다면?

1. ItemStream 의 close() 호출
2. EntityManager 를 close 함으로써, 자원을 종료한다.

---

```java
@Bean
    public ItemReader<? extends Member> customItemReader() {
        HashMap<String, Object> parameters = new HashMap<>();
        parameters.put("firstName", "A%");

        return new JpaCursorItemReaderBuilder<Member>()
                .name("jpaCursorItemReader")
                .entityManagerFactory(entityManagerFactory)
                .queryString("select m from Member m where firstName like :firstName")
                .parameterValues(parameters)
                .build();
    }
```

- `queryString()` : JPQL 을 통해, 내가 원하고자 하는 데이터를 뽑는 Query 를 작성한다.
- `parameterValues()` : Key-Value 값의 HashMap 을 인자로 넣어준다.
  - 이때, Key 는 JPQL 에서 바인딩할 이름하고 동일한 이름 / Value 는 바인딩에 들어갈 값이 들어가면 된다.