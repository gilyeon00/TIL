도커는 레이어들이 쌓여서 이미지를 만들게됨

이때 이전 파일들의 변경여부를 확인한 뒤 레이어를 쌓기때문에 run 단계에서 변함이 없다면
그 이후의 레이어들은 무효화되서 이전에 사용했던 레이어들이 사용됨 (캐싱)

```docker
FROM node
WORKDIR /app
COPY  . /app
RUN npm install
EXPOSE 80
CMD ["node", "server.js"]
```

COPY . /app

: 내 작업 프로젝트를 /app에  복사 ← 프로젝트 통째로 가져오기때문에 package.json의 변경 여부까지는 모름

run npm install 같은 경우 변경 감지 불가 → 항상 install 하게 됨 → 비효율  → 최적화 필요

```docker
FROM node
WORKDIR /app
COPY  package.json /app
RUN npm install
EXPOSE 80
CMD ["node", "server.js"]
```

package.json 의 레이어를 먼저 쌓아놓으면 이걸 기반으로 npm install을 하기 때문에,
변한게 없으면 npm install을 skip 할 수 있게됨
