
# @AuthenticationPrincipal


: 이 어노테이션을 이용해서 로그인 세션 정보를 받아올 수 있다.

### 왜 필요할까?

로그인 사용자의 정보가 필요할 때 매번 서버에 요청을 보내 DB에 접근해서 데이터를 가져오는 것은 비효율적이다. 

따라서 한번 인증된 사용자 정보를 세션에 담아놓고 세션이 유지되는 동안 사용자 객체를 DB로 접근하는 방법 없이 바로 사용할 수 있도록 한다.

**@AuthenticationPrincipal** 어노테이션을 사용하면, `UserDetailService` 의 `loadUserByUsername()` 에서 Return 한 객체를 직접 받아 사용할 수 있다.

⇒ `loadUserByUsername()` 에서 User 를 Return 해줘야한다.