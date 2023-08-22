# 배경

Controller Request 에  `@AuthenticationPrincipal Member member` 포함되어있을 때, 의도는 인증받은 회원(토큰)만 접근가능하도로 하는 건데 swagger 상에서 member를 default 로 다 가져오려한다. → URL이 길어지고, 어차피 필요없는 정보임

# 해결

SwaggerConfig에 다음 내용을 추가해준다.

SpringDocUtils.*getConfig*().addAnnotationsToIgnore(AuthenticationPrincipal.class);

SpringDocUtils.*getConfig*().addAnnotationsToIgnore([무시하고 싶은 어노테이션 명].class);