
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
