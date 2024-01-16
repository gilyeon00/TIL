## 익명볼륨

- 이름 지정 필요 없음, 컨테이너 제거되면 같이 제거됨, 한 번 제거되면 걍 제거되는거임

  컨테이너가 stop 될때는 살아있긴함. 컨테이너끼리 이걸 공유할 수 있음 (너무 휘발성이 강하기때문)

    - **다른 모듈에 의해서 덮어씌워지는 것(← 바인드마운트) 을 방지할 수 있음**
    - 로컬에 폴더 만들게 됨 = docker 가 read-write 레이어 내부에 모든걸 저장안해도됨
    - 성능 향상을 위해 /temp 를 만들수 있음
    - Dockerfile 에서 생성 가능
    - 한 컨테이너에 1: 1

## 명명된 볼륨

- 도커파일에서 생성 할 수 없음
- 컨테이너 실행할 때 -v 옵션으로만 생성 가능
- 특정 컨테이너에 종속적이지 않다 ➡️ 여러개의 컨테이너가 공유해서 사용할 수 있음
    - 컨테이너를 삭제한다고 해서 볼륨이 죽지않는다

## 바인드 마운트

- 특정 컨테이너에 종속적이지 않다
- 로컬의 특정 폴더와 연결 가능 ( 개발자가 주소 지정 )
- 내 로컬 폴더를 삭제하지 않는 이상 docker volume 이 삭제되지않음

---

# Copy

: 코드의 스냅샷을 뜨는 것(실제 복사하도록 명령하는 것 + 그 코드 그 자체의 (레이어들)) 로컬와 컨테이너 사이의 독립적임

# Volume

: 컨테이너가 제거되도 유지된다.

로컬 어딘가에 저장되어있고, docker의 볼륨과 매핑되어있다.

## 익명 볼륨

컨테이너가 종료될때 볼륨도 같이 없어진다.

컨테이너와 익명 볼륨은 1:1 관계이다

```jsx
FROM node:14

WORKDIR /app

COPY package.json /app

RUN npm install

COPY . /app

EXPOSE 80

VOLUME ["/app/feedback"]

CMD ["node", "server.js"]
```

## 네임드 볼륨

컨테이너가 종료되어도 볼륨은 유지된다. (docker에 붙어있는 볼륨은 지워지나, 로컬에 매핑된 볼륨은 지워지지않기 때문)

영구적이어야하는 데이터이지만, 편집하거나 직접 볼 필요가 없는(접근해서 보여주는 용도면 됨) 중요한 데이터에 적합하다.

결론은 매핑된 로컬에 저장되어있는데, 정작 로컬의 경로는 모르기때문에 볼륨에 저장된 파일을 편집할 수 없다.

```jsx
docker run -p 3000:80 -v feedback:/app/feedback -d feedback-node
```

feedback : 볼륨의 이름

:/app/feedback : docker 안의 app 안에 feedback 폴더와 매핑

## 바인드 마운트

기존 : 우리가 소스코드가 변경되어도, 이미지는 변경사항은 반영되지않기 때문에 다시 빌드해서 컨테이너를 띄워야한다.

**볼륨** : 도커에 의해 관리되기때문에 매핑된 로컬 볼륨은 어디에 있는지 알 수 없음

**바인트 마운트** : 매핑된 로컬의 경로를 알 수 있다. (개발자가 지정해줌)

→ 소스 코드도 바인드 마운트에 넣을 수 있다.

⬅️ 스냅샷을 복사하는게 아니라 바인드 마운트에서 복사해서 쓴다면, 항상 최신 코드에 엑세스할 수 있다.

영구적이고 편집가능한 데이터에 적합하다.

### Dockerfile

```java
FROM node:14

WORKDIR /app

COPY package.json /app

RUN npm install

COPY . /app

ENV PORT 80

EXPOSE $PORT

# VOLUME ["/app/feedback"]

CMD ["node", "server.js"]
```

### .env

```java
PORT=80
```

### CLI

```java
docker run -p 3000:8000 --rm -d 
		--name feedback-app 
		-v feedback:/app/feedback 
		-v "/Users/gilyeon/Desktop/study/Docker/data-volumes-01-starting-setup:/app" 
		-v /app/node_modules 
		-v /app/temp 
		--env PORT=8000 feedback-node-env
```