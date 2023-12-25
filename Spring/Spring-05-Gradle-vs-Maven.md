**Java 프로젝트를 빌드, 테스트, 배포하기 위한 오픈소스 빌드 도구**

# 빌드 관리 도구?

작성한 java 코드와 프로젝트 내에 필요한 각종 xml, properties, jar 파일들을 JVM이나 WAS가 인식할 수 있도록 패키징 해주는 빌드 과정 == ‘빌드 자동화 도구’

다양한 외부 라이브러리들을 모두 다운받을 필요없이, 빌드도구 설정파일에 라이브러리 정보 명시
→ 자동 다운로드 및 관리

## Maven

**pom.xml** 파일에 프로젝트에 필요한 각종 의존성들과 각 라이브러리들의 버전 명시 등의 **프로젝트 설정 정보를 xml 형식으로 길게 나열해서 작성**

Maven의 단점

- XML 구성 파일이 복잡하고 가독성이 떨어짐
- 빌드 성능이 느림

```sql
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
    <version>2.2.4.RELEASE</version>
</dependency>
```

## Gradle

**빌드 캐시를 사용**하여 **이전 빌드에서 변경이 없는 경우 이전 빌드 결과를 재사용하여 빌드 시간을 단축**

Groovy 언어를 사용한 Domain-specific-language(**DSL**)를 사용
(설정파일을 xml파일을 사용하는 Maven보다 코드가 훨씬 간결)

Gradle의 단점

- Maven보다 학습 곡선이 높음
- Maven보다 좀 더 복잡한 구조와 설정 방법

```sql
implementation group: 'org.springframework.boot', name: 'spring-boot-starter-data-jpa', version: '2.2.4.RELEASE'
```

## 결론

**일단 Gradle 사용하자**

레거시한 프로젝트는 maven으로도 많이 사용하기 때문에 gradle사용해보고 나중에 maven해보는 걸 추천

### Gradle Plugin

[Gradle - Plugins](https://plugins.gradle.org/)