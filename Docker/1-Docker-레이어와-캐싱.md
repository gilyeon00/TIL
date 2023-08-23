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

# Attached & Detached

`docker start` default : Detached mode (명령어 수행했을 때 백그라운드로 실행)

- Attached mode 접근 방법 :

  docker attach [컨테이너 ID] : attached 모드 진입

  docker logs [컨테이너 ID] : 이전 로그 출력


`docker run` default : Attached mode (명령어 수행했을 때 포어그라운드로 실행)

```python
from random import randint

min_number = int(input('Please enter the min number: '))
max_number = int(input('Please enter the max number: '))

if (max_number < min_number): 
  print('Invalid input - shutting down...')
else:
  rnd_number = randint(min_number, max_number)
  print(rnd_number)
```

- Docker는 웹서버만을 띄울 수 있는 것뿐만아니라, 이렇게 간단한 것도 띄울 수 있다.
- console 인터페이스를 통해 사용자와 소통을 할 수있다.

```docker
FROM python

WORKDIR /app

COPY . /app

CMD ["python", "rng.py"]
```

docker build .

docker run [이미지 ID] → 에러 난다.

- 이유  : 컨테이너는 사용자랑 분리되어있으므로, 어떻게 console 로 받는지
- 해결 : 인터렉티브 모드 사용

  docker run -i [이미지 ID]

  docker run -t (TTY)

  docker start -i [컨테이너 ID]


docker start -i : 인터렉티브 모드 (사용자와의 상호작용)

docker start -a : 단순 attached 모드 (사용자와의 상호작용은 염두에 없음, 실시간으로 로그보겠다)

<img width="866" alt="스크린샷 2023-08-21 오후 12 57 04" src="https://github.com/gilyeon00/TIL/assets/52391627/75afa620-11e6-4e8d-a191-fe7b30b67e79">
