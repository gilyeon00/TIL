# isLoading VS isFetching

- **isLoading 이 isFetching 보다 큰 범위**
- **`isLoading`** :
    - ***캐시된 데이터조차 없이***, ***처음 실행된 쿼리***
    - isFetching 이 true 이고 !
    - 최초에 사용
- **`isFetching`** :
    - 쿼리함수가 해결안되었을 때의 상태
    - 리패칭 사용

즉, 결론적으로 `isLoading`과 `isFetching`은 비슷하게 **'로딩'** 이라는 개념을 사용하지만 ***기존에 캐시된 데이터가 있느냐*** 에 따라 다르다.