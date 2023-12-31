# Web Server vs WAS

> 상황에 따라 변하는 정보를 제공할 수 있는가.


### Web

인터넷을 기반으로 한 정보를 공유, 검색할 수 있게하는 서비스

URL(주소) +  HTTP(통신규칙) + HTML(내용)

### Server

클라이언트에게

웹 서버 ← html, css, 이미지와 같이 정적인 데이터만 보여줄 수 있음

동적인걸 보여주고 싶음 → WAS의 탄생

# Web Server

Apache

## Web Application

웹에서 실행되는 응용 프로그램

# 📌 WAS     : Web Application Server

php jsp와 같은 언어들을 사용해서 동젓인 페이지를 만들 수 있는 서버

프로그램 실행환경과 데이터베이스 접속 기능 제공

비즈니스 로직 수행 가능

웹 서버 + 웹 컨테이너

tomcat

1. client가 WAS로 요청보냄
2. 정적인지 동적요청인지 확인
3. 동적요청이다 → Web Container에 전달후 동적 컨텐츠 생성
4. Web Container에서 Web Server로 보냄

# Proxy Server

[[Network] 프록시 서버란? (feat. 필요한 이유) (What is a Proxy server?)](https://fomaios.tistory.com/entry/Network-%ED%94%84%EB%A1%9D%EC%8B%9C-%EC%84%9C%EB%B2%84%EB%9E%80-feat-%ED%95%84%EC%9A%94%ED%95%9C-%EC%9D%B4%EC%9C%A0-What-is-a-Proxy-server)