## fallback 데이터

```java
const fallback = [];
  const { data: appointments = fallback } = useQuery(
    queryKeys.appointments,
    () => getAppointments(monthYear.year, monthYear.month),
  );
```

- `**fallback**`
    - 커스텀 훅 하나하나에서 조기 반환을 막을 수 있도록, 기본 값을 넣어주는 용도
    - 데이터가 없는 상태에서 map 돌릴라하면 에러가 난다. (undefined) 그렇기 때문에 기본값으로 빈 배열을 넣는다
    - isLoading 과 isFetching 을 중앙에서 집중 관리하기 위해서
- data: appointments
    - data 에 이름(appointments)을 붙여주기