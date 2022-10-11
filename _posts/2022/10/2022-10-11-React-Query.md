---
title: React Query
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-10-11
categories: [Frontend]
tags: [react query]
math: true
mermaid: true
---
## React-Query란?

React 애플리케이션에서 데이터를 가져오기 위한 라이브러리입니다.

데이터 Fetching, 캐싱, 동기화, 서버 쪽 데이터 업데이트 비동기 과정에서 데이터를 관리하기 편하게 할 수 있습니다.

## 사용하는 이유

아래와 같이 React는 데이터를 가져올 때 useEffect를 사용하고 useState를 사용하여 상태를 유지합니다.

```js
import React, { useEffect, useState } from "react";
import axios from "axios";

const App = function () {
  const [sate, setState] = useState([]);

  const load = async () => {
    const { data } = await axios.get("http://localhost:4000/api");

    setState(data);
  };

  useEffect(() => {
    load();
  }, []);

  return <div></div>;
};
```

만약 앱 전체에 데이터가 필요한 경우 상태 관리 라이브러리(Redux, Recoil 등)를 사용하게 됩니다.

기존에 다양한 상태 관리 라이브러리 들은 비동기적 작업보다는 동기적인 클라이언트 데이터를 관리하기에 더 적합하기 때문에

**react-query를 사용함으로 서버와 클라이언트 데이터를 분리합니다.** 

-   client state: 앱 메모리에 유지되고 액세스 하거나 업데이트하는 것 (동기적)
-   server state: 업데이트에 필요한 API(비동기)

## 장점

-   캐싱 처리가 더 간단해집니다.
-   프로젝트 구조가 기존보다 단순해집니다. 때문에 유지 보수하고 새로운 기능을 추가하기 쉽습니다.
-   직접 만들어서 사용했던 기타 기능들을 옵션으로 지원합니다.
-   같은 데이터를 여러 번 요청할 경우 한 번만 요청하고 옵션에 따라 중복 호출 허용 시간 조절 가능합니다.
-   백그라운드에서 stale 한 상태 데이터를 refetch 합니다.
-   서버 쪽 데이터를 가비지 컬렉션을 이용하여 자동으로 메모리 관리를 해줍니다.
-   get을 한 데이터에 대해 update를 하면 자동으로 get을 다시 수행합니다.  
    예를 들어, 게시판의 글의 목록을 가져오고 게시판의 글을 생성하면 게시판 글을 get 하는 api를 자동으로 실행합니다.

## React-Query 사용하기

### Install

```bash
npm i react-query
```

react-query를 사용할 수 있도록 세팅합니다.

```js
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import { HomePage } from "./components/Home.page";
import { QueryClient, QueryClientProvider } from "react-query";
import { ReactQueryDevtools } from "react-query/devtools";

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Router>
          <Routes>
            <Route path="/" element={<HomePage />} />
          </Routes>
        </div>
      </Router>
      <ReactQueryDevtools initialIsOpen={false} position={'bottom-right'}/>
    </QueryClientProvider>
  );
}

export default App;
```

```js
import { useQuery } from "react-query";
import axios from "axios";

const Page = () => {
  const fetch = () => {
    return axios.get("http://localhost:4000/api");
  };
  const { isLoading, data, isError, error } = useQuery(
    "key",
    fetch,
  );

  if (isLoading) {
    return <h2>Loading...</h2>;
  }

  if (isError) {
    return <h2>{error.message}</h2>;
  }

  return (
    <>
      <h2>Hello world</h2>
      {data?.data.map((el) => {
        return <div key={el.id}>{el.name}</div>;
      })}
    </>
  );
};
```

-   첫 번째 파라미터로 쿼리에 사용할 유니크한 키값을 넣어줍니다.
-   두 번째 파라미터로 쿼리가 데이터를 요청하는 데 사용할 비동기 함수(api)를 인자로 넣습니다.
-   데이터 또는 오류를 반환해야하고 데이터는 undefined가 될 수 없습니다.

### 변화가 있을 때 refetch

```js
import React, { useState } from "react";
import { useQuery } from "react-query";
import axios from "axios";

const App = function () {
  const [id, setId] = useState(1);

  const load = async () => {
    const resp = await axios.get(`http://localhost:4000/${id}`);
    return resp;
  };

  const { data, status } = useQuery(["key", id], load);
  ...
};
```

-   첫 번째 파라미터를 배열을 넣습니다.
-   0번째 인덱스에 유니크한 키값을 넣고, 1번째 인덱스부터 변화가 생길 때마다 refetch 되어야 하는 구성 요소들을 나열합니다.

### 옵션 설정

세 번째 인자로 옵션을 설정할 수 있습니다.

```js
const { data, status, isLoading, isFetching } = useQuery(
  ["key", currentPage],
  load,
  {
    refetchOnWindowFocus: false,
    statleTime: 10000,
    cashTime: 20000,
  }
);
```

-   **refetchOnwindowFocus (default: true)**
    -   boolean | 'always' | ((query: Query) => boolean | "always")
    -   브라우저가 포커스에 되었을 때 refetch 여부를 boolean으로 설정할 수 있습니다.
    -   true로 설정하면 데이터가 오래된 경우 쿼리가 창 포커스에서 다시 가져옵니다.
    -   false로 설정하면 쿼리가 창 포커스에서 다시 가져오지 않습니다.
    -   "always"로 설정하면 쿼리는 항상 창 포커스에서 다시 가져옵니다.
    -   함수를 설정하면 값을 계산하는 쿼리와 함께 함수가 실행됩니다.
-   **refetchOnMount (default: true)**
    -   boolean | "always" | ((query: Query) => boolean | "always")
    -   새로 마운트가 되었을 때 refetch 여부를 설정할 수 있습니다.
    -   true로 설정하면 데이터가 오래된 경우 쿼리가 마운트 시 다시 가져옵니다.
    -   false로 설정하면 쿼리가 마운트 시 다시 가져오지 않습니다.
    -   "always"로 설정하면 하면 쿼리가 마운트 시 항상 다시 가져옵니다.
    -   함수를 설정하면 값을 계산하는 쿼리와 함께 함수가 실행됩니다.
-   **refetchOnReconnect (default: true)**
    -   boolean | "always" | ((query: Query) => boolean | "always")
    -   네트워크가 끊어졌다가 다시 연결될 때 refetch 여부 설정할 수 있습니다.
    -   true로 설정하면 데이터가 오래된 경우 쿼리가 다시 연결할 때 다시 가져옵니다.
    -   false로 설정하면 네트워크를 다시 연결할 때 쿼리를 다시 가져오지 않습니다.
    -   "always"로 설정하면 쿼리는 네트워크를 다시 연결할 때 항상 다시 가져옵니다.
    -   함수를 설정하면 값을 계산하는 쿼리와 함께 함수가 실행됩니다.
-   **refetchInterval**  
    -   number | false | ((data: TData | undefined, query: Query) => number | false)
    -   데이터를 지정한 시간에 따라 refetch를 시켜주는 옵션입니다.
-   **refetchIntervalInBackground**
    -   boolean 
    -   true로 설정하면 background에 있어도 refetchInterval에 의해 refetch 됩니다.
-   **staleTime (default: 0)**
    -   number | Infinity
    -   데이터가 fresh 한 상태에서 오래된 상태로 변경되는 시간을 설정합니다.
    -   fresh 상태일 때는 쿼리 인스턴스가 새롭게 mount 되어도 네트워크 fetch가 일어나지 않습니다.
    -   데이터가 한번 fetch 되고 나서 staleTime이 지나지 않았다면 mount 되어도 fetch가 일어나지 않습니다.
    -   Infinity로 설정하면 데이터가 오래된 것으로 간주되지 않습니다.
-   **cashTime (default: 5분)**
    -   데이터가 비활성화 상태일 때 캐싱된 상태로 남아있는 시간입니다.
    -   쿼리 인스턴스가 unmount 되면 데이터는 비활성화 상태로 변경되며, 캐시는 cacheTime만큼 유지됩니다.
    -   cacheTime이 지나면 가비지 콜렉터로 수집됩니다.
    -   cacheTime이 지나기 전에 쿼리 인스턴스가 다시 마운트 되면, 데이터를 fetch 하는 동안 캐시 데이터를 보여줍니다.
    -   cacheTime은 staleTime과 관계없이, casheTime이 지나면  inactive 된 시점을 기준으로 캐시 데이터 삭제를 결정합니다.
-   **isFetching**
    -   데이터가 fetch 될 때 true or 캐싱 데이터가 있어서 백그라운드에서 fetch 될 때 true 반환합니다.
-   **isLoading**  
    -   캐싱된 데이터가 없고 fetch 중일 때 true, 아닐 때 false 반환합니다.

```js
useQuery("names", fetch, {
  onSuccess: (data) => {
    console.log(data);
  },
  onError,
  select: (data) => {
    const names = data.data.map((el) => el.name);
    return names;
  },
});
```

-    **onSuccess**
    -   이 함수는 쿼리가 새 데이터를 성공적으로 가져올 때마다 실행됩니다.
-   **onError**
    -   이 함수는 쿼리에 오류가 발생하고 오류가 전달되면 실행됩니다.
-   **select**
    -   (data: TData) => unknown 
    -   비동기 호출에서 반환되는 데이터를 재구성할 수 있습니다.

```js
useQuery(
    "key",
    fetchSuperHeroes,
    {
      initialData: () => {
        return {
          data: [
            {
              id: 2,
              name: "test",
            },
          ],
        };
      },
    }
  );
```

-   **initialData**
    -   TData | () => TData
    -   초기값을 설정할 수 있습니다. (쿼리가 아직 생성되거나 캐시되지 않은 경우)
    -   캐시에 유지됩니다.

```js
const { refetch } = useQuery("key", fetch, {
  enabled: false,
});
```

-   **enabled**
    -   boolean값으로 쿼리를  자동으로 실행 되지 않도록 설정할 수 있습니다.
-   **refetch**
    -   쿼리를 수동으로 다시 가져오는 기능입니다.

```js
const { isLoading, isError, error, data, isFetching } = useQuery(
  ["colors", pageNumber],
  () => fetchColors(pageNumber),
  {
    keepPreviousData: true,
  }
);
```

-   **keepPreviousData**
    -   boolean
    -   새 데이터를 가져올 때 이전 데이터가 유지되고 새 데이터가 도착하면 이전 데이터와 스왑 되어 새 데이터를 표시합니다.
    -   isPreviousData로 현재의 제공된 데이터의 상태를 알 수 있습니다.

#### useInfinitedQuery

무한 스크롤 또는 더보기와 같은 목록을 로드할 때 유용합니다.

```js
const {
  isLoading,
  isError,
  error,
  data,
  fetchNextPage,
  hasNextPage,
  isFetching,
  isFetchingNextPage,
} = useInfiniteQuery(["colors"], fetchColors, {
  getNextPageParam: (_lastPage, pages) => {
    if (pages.length < 4) {
      return pages.length + 1;
    } else {
      return undefined;
    }
  },
});
```

-   **getNextPageParam**
    -   다음 페이지에 호출할 pageParam 값을 반환할 수 있습니다.
    -   getNextPageParams undefined일 때 hasNextPage는 false를 반환하고 그 외 다른 값을 반환할 때,  
        hasNextPage는 true를 반환합니다.
-    **hasNextPage**
    -   다음 페이지가 있는지 여부를 boolean으로 확인할 수 있습니다.
-   **fetchNextPage**
    -   더보기 버튼 클릭 or 무한 스크롤 시 호출할 때 사용됩니다.
-   **isFetchingNextPage**
    -   isFetchingNextPage와 isFetchPreviousPage로 백그라운드 새로 고침 상태와 추가 로딩 상태를 구분할 수 있습니다.

### 비동기 여러 개를 실행할 경우 useQueries

 useQueriy를 Promise.all처럼 사용할 수 있는 것이 useQueries입니다.

```js
useQueries([
  {
    queryKey: ["id", id],
    queryFn: () => loadIDData(),
  },
  {
    queryKey: ["gender", gender],
    queryFn: () => loadGenderData(),
  },
]);
```

### QueryCache

쿼리에 대해 성공 및 실패 전처리를 할 수 있습니다.

```js
const queryClient = new QueryClient({
  queryCache: new QueryCache({
    onError: (error, query) => {
      console.log(error, query);
    },
    onSuccess: (data) => {
      console.log(data);
    },
  }),
});
```

## useMutation

데이터를 생성, 업데이트 또는 삭제할 때 사용하기 유용합니다.

```js
// Access the client
const queryClient = useQueryClient();

// Queries
const query = useQuery(["todos"], getTodos);

// Mutations
const mutation = useMutation(postTodo, {
  onSuccess: () => {
    // Invalidate and refetch
    queryClient.invalidateQueries(["todos"]);
  },
});
```

-   mutate는 useMutation을 이용해 작성한 내용들이 실행될 수 있도록 도와주는 trigger 역할을 합니다.

[React-Query 공식 홈페이지](https://tanstack.com/query/v4/docs/reference/useQuery?from=reactQueryV3&original=https://react-query-v3.tanstack.com/reference/useQuery)
