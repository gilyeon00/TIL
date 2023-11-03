# Scope

- Spring container 에서 Bean 이 관리되는 범위
- singleton, prototype, request, session, application 이 있으며, 기본은 singleton 으로 생성된다

> **Singleton (싱글톤) 이란**
>

메모리에 하나만 존재하는 것

객체를 만들 때마다, 새로운 객체가 생기는 것이 아니라 메모리에 하나만 존재하므로 해당 객체를 참조한다.

**Prototype**

- 참조할 때 마다 생성되는 것

**Request, Session, Application**

- 웹에서 사용하는 범위
- Request : 요청과 응답의 생성과 소멸 주기
- Session : 사용자의 session 범위
- Application : 글로벌

## @JobScope, @StepScope

- Job 과 Step 의 Bean 생성과 실행에 관여하는 Scope
- Proxy 모드를 기본값으로 하는 Scope 이다. (일반 Bean 객체가 아닌, Proxy 객체)
    - @Scope(value = “job”, proxyMode = ScopedProxyMode.TARGET_CLASS)
- 해당 Scope 가 선언되면, Bean 생성이 어플리케이션 구동시점이 아니라 **Bean 의 실행 시점**에 이뤄진다.
    - 원래 Bean 객체는 Spring 이 구동될 때, 객체가 생성되고 Spring Container 에 의해 관리된다.
- Proxy 모드로 Bean 이 선언되기 때문에, 어플리케이션 구동 시점에는 Bean 의 Proxy 객체가 생성되어, 실행 시점에 실제 Bean 을 호출한다.
- 병렬처리 시, 각 스레드마다 생성된 Scope Bean 이 할당되기 때문에 스레드에서 안전하게 실행할 수 있다.
    - 원래 싱글톤이라면, Bean 은 하나만 만들어져서 다른 곳에서 참조해도 무조건 그 해당 Bean 을 보게된다.
      하지만 Proxy 모드일 경우, 한 객체가 각 스레드에서 호출될때 Bean 이 생성된다. 이때 Bean 은 각 스레드에만 영향이 있다.
- **방법**
    - @Value 어노테이션을 이용해서, Bean 의 실행 시점에 값을 참조할 수 있게한다. ➡️ 일종의 Lazy Binding
        - @Value(”#{jobParameters[파라미터명]}”),
        - @Value(”#{jobExecutionContext[파라미터명]”})
        - @Value(”#{stepExecutionContext[파라미터명]”})
    - @Value 어노테이션을 사용할 경우, 선언문에 @JobScope, @StepScope 를 꼭 정의해야함 ❗️

### @JobScope

- Step 선언문에 정의
- @Value : jobParameter, jobExecutionContext 만 사용가능

### @StepScope

- Tasklet 이나 ItemReader, ItemWriter, ItemProcessor 선언문에 정의
- @Value : jobParameter, jobExecutionContext, stepExecutionContext 사용 가능