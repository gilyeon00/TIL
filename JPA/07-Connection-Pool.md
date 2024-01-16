# 1. 커넥션 풀(DBCP)이란?
웹 컨테이너(WAS)가 실행되면서 DB와 미리 connection(연결)을 해놓은 객체들을 pool에 저장해두었다가.

클라이언트 요청이 오면 connection을 빌려주고, 처리가 끝나면 다시 connection을 반납받아 pool에 저장하는 방식을 말합니다.

# 2.커넥션 풀(DBCP) 특징
웹 컨테이너(WAS)가 실행되면서 connection 객체를 미리 pool에 생성해 둡니다.
HTTP 요청에 따라 pool에서 connection객체를 가져다 쓰고 반환한다.
이와 같은 방식으로 물리적인 데이터베이스 connection(연결) 부하를 줄이고 연결 관리 한다.
pool에 미리 connection이 생성되어 있기 때문에 connection을 생성하는 데 드는 요정 마다 연결 시간이 소비되지 않는다.
커넥션을 계속해서 재사용하기 때문에 생성되는 커넥션 수를 제한적으로 설정함
