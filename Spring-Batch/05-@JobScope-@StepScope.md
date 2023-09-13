# @JopScope 와 @StepScope

`@JobScope`와 `@StepScope`는 스프링의 기본 Scope인 `싱글톤` 방식과는 대치되는 역할

Bean의 생성 시점이 스프링 애플리케이션이 실행되는 시점이 아닌 @JobScope, @StepScope가 명시된 메서드가 실행될 때까지 지연시키는 것을 의미한다.

이러한 행위를 `Late Binding`이라고도 한다.

- `정적 바인딩`(static binding) :
    - 실행 시간 전에 일어나고, 실행 시간에는 변하지 않은 상태로 유지되는 바인딩
    - 오버로딩(Overloading) ← 같은 이름 메서드 다르게 정의
        - 컴파일 과정에서 어떤 메서드를 호출할 지 결정
    - private, final, static
- `동적 바인딩`(dynamic binding) :
    - 실행 시간에 이루어지거나 실행 시간에 변경되는 바인딩.  = 늦은 바인딩(late binding)
    - 오버라이딩(Overriding)  ← 상속 관계에서 같은 이름 재정의
        - 실행 시간에 어떤 메서드를 호출할 지 결정

          객체가 부모 객체가 될 지,  자식 객체가 될 지 타입을 확정지을 수 없기때문

    - 다형성, 상속이 가능한 이유