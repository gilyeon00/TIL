## 🧐 개인 PC에 node 를 깔기는 싫은데, node 기반 프로젝트를 하려면??

package.json 을 수작업으로 일일히 작성하기에는 말도안된다. `npm init`명령어가 필요하다 ❗️

➡️Docker 를 사용해서 최소한의 환경을 만들어주고, 로컬로 미러링을 해서 node 설치가 필요없도록 할 수 있다.

- `Dockerfile`
    - 최대한 가벼운 환경을 위한 14-alpine 이미지 사용, 작업 디렉터리만 지정해준다.

        ```
        FROM node:14-alpine
        
        WORKDIR /app
        ```

- `바인드 마운트` + `npm init` 명령어 넘겨주기

```java
docker run -it -v /Users/gilyeon/Desktop/study/Docker/docker-utility:/app node-util npm init
```

## 🍯 결과

로컬에서 npm init 을 한 것처럼, package.json 파일이 생겼다 !

이로써 로컬에 node 를 설치하지 않고도, node 프로젝트를 할 수 있당

⭐️ 컨테이너에서 실수로 모든 것을 삭제하는 명령어를 실행해도 바인드 마운트로 인해 호스트 머신에서도 삭제되는 경우를 방지할 수 있다.

→ 바인드 마운트는 로컬에서 삭제해야지, 컨테이너에서 삭제해봤자 크게 의미없다

- 바인드 마운트는 유틸리티 컨테이너와 함께 구성파일에 자주 사용됨

```java
version: '3.8'
services:
  npm:
    build: ./
    tty: true
    stdin_open: true
    volumes:
      - ./:/app

volumes:
  app:
```