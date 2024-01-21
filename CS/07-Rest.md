# REST(`RE`presentational `S`tate `T`ransfer)

: HTTP 통신에서 어떤 자원에 대한 CRUD 요청을 resource 와 Method 로 표현하여 특정한 형태로 전달하는 방식

➡️ REST 란 어떤 자원에 대해 `CRUD(Create, Read, Update, Delete)` 연산을 수행하기 위해 `URI(Resource)` 로 요청을 보내는 것으로, Get, Post 등의 방식(Method) 을 사용하여 요청을 보내며, 요청을 위한 자원은 특정한 형태(Json, xml…)로 표현된다.

이런 REST 기반의 API 를 웹으로 구현한 것이 **RESTful API** 이다.

```java
Post http://localhost:8080/board
```

요청을 위한 자원(board) + Method(행위 Get, Post..) + 특정한 형태의 자원(Json) ⇒ REST