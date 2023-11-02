refetching 을 하려면 데이터가 stale 상태여야한다 !

(서버와 데이터가 다를 수도 있다는 상태)

staleTime 의 기본값은 0초

- 이유 : 항상 만료 상태니까, 서버에서 데이터를 가져와야된다는 뜻 (최신값을 항상 유지하기 위해ㅖ)
- 설정해놓은 시간동안은 fresh 한 데이터이다.

cacheTime : 기본값은 5분

- 캐싱된 데이터
- 리패칭 될때(서버에서 다시 데이터가져올때) 쓰이는 데이터

stale 쿼리가 자동적으오 다시 가져오는 조건

- 윈도우 다시 포커싱
- 쿼리키가 처음 호출되는 경우

refetching 을 하기위해서는 옵션으로 제어가능

- 일반적인 경우 : 전역
- 호출 쿼리 사용에 특정적인것

직접 리패칭하고 싶다면?

- useQuery 를 사용해서 refetch 함수를 반환
    - useQeury/invaild~..
    - refetch()

리패칭을 제한 하고 싶다면?

- stale time 증가 시키기

```jsx
export function useTreatments(): Treatment[] {
  const fallback = [];
  const toast = useCustomToast();
  const { data = fallback } = useQuery(queryKeys.treatments, getTreatments, {
    staleTime: 60000,
    cacheTime: 90000,
    retryOnMount: false,
    refetchOnWindowFocus: false,
    refetchOnReconnect: false,
  });
  return data;
}
```

전역으로 하고 싶다면?

queryClient 옵션에 넣어주면 된다 !

```jsx
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      onError: queryErrorHandler,
      staleTime: 60000,
      cacheTime: 90000,
      retryOnMount: false,
      refetchOnWindowFocus: false,
      refetchOnReconnect: false,
    },
  },
});
```