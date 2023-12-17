# ****@NotNull****

`@NotNull`은 이름 그대로 `Null만` 허용하지 않는다.

`""`이나`" "`은 허용

# ****@NotEmpty****

`@NotEmpty`는`null`과`""`둘 다 허용하지 않는다.

`null`과`""`은 허용하지 않되,`" "`은 허용

# ****@NotBlank****

`@NotBlank`는`null`과`""`과`" "`모두 허용하지 않는다.

---

예외처리는 entity 가 아닌, DTO 에서 막아줘야한다.