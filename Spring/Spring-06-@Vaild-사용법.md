## 배경

RequestDto 로부터 받아오는 값을 일일히 null 체크해서 검증하기에 귀찮다
@NotNull, @NotBlank 와 같은 어노테이션을 사용해볼까?

## 과정

@NotNull, @NotBlank 와 같은 어노테이션을 사용해서 Request 를 받아올때 검증을 하고 싶다 !

### @NotNull

- null 값 체크
- Integer, enum

### @NotEmpty

- null, “” 체크

### @NotBlank

- null, “”, “ “ 체크
- String

- 잘못사용하면 생길 수 있는 에러 메세지 ( @NotNull 을 사용해야할 곳에 @NotBlank 를 사용했을 경우 )

<aside>
💡 No validator could be found for constraint 'jakarta.validation.constraints.NotBlank'

</aside>

붙여준다고 끝이 아니라, RequestBody 를 검증해야하므로, Controller 의 RequestBody 앞에 @Valid 어노테이션을 붙여준다.

이때 ⭐️ 다음 라이브러리를 build.gradle 에 넣어줘야 해당 어노테이션이 잘 작동한다 !

```yaml
implementation group: 'org.springframework.boot', name: 'spring-boot-starter-validation', version: '2.5.4'
```

```java
@PostMapping("")
public ResponseEntity<ResponseDto> create(
        @AuthenticationPrincipal Member member
        , ***@Valid*** @RequestBody MemberRequestDto memberRequestDto
){
	...
}
```