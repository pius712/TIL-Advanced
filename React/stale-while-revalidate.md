# 유저를 기다리게 하지마라 - 리액트 hooks


참고한 자료
- (https://www.toptal.com/react-hooks/stale-while-revalidate](https://www.toptal.com/react-hooks/stale-while-revalidate)
- (https://web.dev/stale-while-revalidate/)[https://web.dev/stale-while-revalidate/]
- (https://jbee.io/react/thinking-about-global-state/)[https://jbee.io/react/thinking-about-global-state/]


- 인트로
- 일반 data fetching hooks
- stale-while-revalidate 개념의 custom hooks

## 인트로 

http 스펙에 `cache-control : stale-while-revalidate` 이라는 헤더가 있습니다. 


캐시에 관한 내용인데, 대략적인 내용은 아래와 같습니다. 
1. request
2. response가 브라우저에 캐싱되어 있는가? 
    2-1. 안되어 있으면 리퀘스트를 보낸다.
   
3. 캐싱이 되어 있는 경우 우선, 캐싱된 데이터를 사용한다. 
4. 캐싱이 expire된 경우, 리퀘스트를 보내고 캐시를 업데이트한다. 

이는 assets(이미지, 아이콘 등)을 사용할 때, 이를 cache-control을 통하여 사용하는 기법은 널리 사용되고 있습니다.
이는 일반 api 호출에서는 잘 사용되지 않는데,
이는, cache-control을 위한 적절한 값을 알기 어렵기 때문입니다.
이로 인해서 대다수의 application은 매번 새로운 요청을 보내고, 새로운 응답을 받는 방식으로 구현되어 있습니다.

이와 비슷한 개념을 리액트의 데이터 fetching 모델에 사용하는 라이브러리가 아래와 같은 것들이 있습니다. 

- react-query
- swr 

custom hooks를 통해서 이러한 개념의 데이터 fetching이 어떻게 구현되어있는지 오늘 살펴 보려고 합니다.

## 일반 data fetching hooks

우선적으로 일반적으로 data fetching을 하는 Custom hooks에 대해서 알아보겠습니다. 


```javascript
import React, { Fragment, useState, useEffect } from 'react';
import axios from 'axios';

const useDataApi = (initialUrl, initialData) => {
   const [data, setData] = useState(initialData);
   const [url, setUrl] = useState(initialUrl);
   const [isLoading, setIsLoading] = useState(false);
   const [isError, setIsError] = useState(false);
   
   useEffect(() => {
       (async () => {
           let isCancelled = false;
           setIsError(false);
           setIsLoading(true);

           try {
               const result = await axios(url);
               if (!isCancelled) {
                   setData(result.data);
               }
           } catch (error) {
               if(!isCancelled) {
                   setIsError(true);
               }
           }

           setIsLoading(false);
       })();
   }, [url]);
    return [{ data, isLoading, isError }, setUrl];
};
```


## stale-while-revalidate 개념의 custom hooks

이를 react hooks를 통해 개선하는 방법에 대해 알아보고자 한다. 

- 첫 request일 때, 이를 cache로 저장하고, 받아온 값을 반환한다.
- 이후 api 호출 시, 캐싱된 데이터를 즉각 반환한 뒤,
- api를 비동기 호출을 하고, 결과 값이 오면, UI를 변경하고 cache에 저장한다. 

### 초기 모델

1. 우선 캐싱을 위한 객체를 만들어 줍니다. 캐싱을 위해서는 `key` 값으로 으로 캐싱된 값을 구분할 수 있어야 하는데, 
우선 초기 모델에서는 key를 인자로 받도록 하겠습니다. 
2. request를 보내기 전에, 캐시를 먼저 확인하고 이를 반환합니다. 
     
3. 그리고 request를 보내서 캐시를 업데이트해주고, state를 업데이트 해줍니다. 

```javascript
// hooks/useQuery.js
const cache = {};
const useQuery = (key) =>{ 
    const [data, setData] = useState(null);
    useEffect(()=>{ 
        
        if (cache[key] !== undefined) {
            setData(cache[key]);
        }
        (async () =>{
            const response = await get('/users');
            cache[key] = response.data;
            setData(cache[url]);
        })();
    }, [url])
    
    return data;
}
```

여기서 질문이 있을수도 있을 것 같습니다. 

**Q. `setData()`는 배치로 동작하지 않나요? 그러면 의미가 없을거 같은데?**

A. 일반적으로 `setState` 는 배치로 동작하는 것이 맞습니다. 하지만 이는 리액트의 이벤트 핸들러의 경우에 해당 방식과 같이 동작하는데요, 
(링크)[https://github.com/facebook/react/issues/14259]를 보시면 아시겠지만, setTimeout, promise, 
async/await 등에서는 배치로 동작하지 않습니다.

**Q. 해당 쿼리를 지속적으로 호출한다면, cache 메모리 크기에 문제가 있지 않을까요?**

A. 아래에서 delete 하는 로직을 추가할 예정입니다. 


### 일반화 모델 

 위의 모델을 조금 더 일반화해 보려고 합니다. 

- url을 입력받아, 여러 엔드포인트에 접근할 수 있도록 한다.
  우선, 여러개의 컴포넌트에서 사용할 수 있기 위해서 `url`을 입력을 받아 호출을 하려고 합니다.
  그리고 이를 `key`로 사용할 것입니다.
  
- loading과 error 상태를 추가
  위의 일반 data fetching hooks와 마찬가지로 loading, error 상태를 추가하고 이를 구현합니다. 

- race condition 관련 로직 추가 

```javascript
// hooks/useQuery.js
const cache = {};
const useQuery = (url) =>{ 
    const [data, setData] = useState(null);
    const [isLoading, setIsLoading] = useState(false);
    const [isError, setIsError] = useState(false);
    
    useEffect(()=>{
        let isCancelled = false;
        setIsError(false);
        setIsLoading(true);
        if (cache[url] !== undefined && !isCancelled) {
            setData(cache[url]);
        }
        (async () =>{
            try {
                const response = await get(url);
                cache[url] = response.data;
                if(!isCancelled) {
                    setData(cache[url]);
                }
            }catch(error) {
                if(!isCancelled) {
                    setIsError(true);
                }
            }
            setIsLoading(false);
        })();
    }, [url])
    
    return data;
}
```

When a request is sent to the API server endpoint for the first time, cache the response and then return it.
The next time that same API request happens, use the cached response immediately.
Then, send the request asynchronously to fetch a new response. When the response arrives, asynchronously propagate changes to the UI and update the cache.

### 최적화 모델

