# useEffect - Race Condition 다루기

useEffect를 내에서 데이터 요청을 하는 로직은 `race condition`이 발생할 수 있다. 

예를들어, id에 따라 데이터를 다르게 비춰주는 컴포넌트가 있다고 할 때,

아래의 useEffect 훅에서 `{ id: 10 }` `{ id: 20 }` 이 연속으로 호출 된다고 가정해보자. 

```javascript
useEffect(() => {
    (async () =>{ 
        const userProfile = await get(url, id);
        setProfile(userProfile);
    })
}, [id]);
```

위의 경우 두 개의 요청이 순차적으로 일어나지 않고, 병렬적으로 수행되게 되는데, 이 때 race condition이 발생하게 된다.
만약, { id: 10 }인 유저의 프로필이 늦게 응답이 오는 경우,
현재 { id : 20 } 인 상황인데, 데이터는 { id : 10 } 유저의 데이터 프로필이 세팅되게 된다. 

이러한 race condition은 아래의 이유로 인해 발생한다. 

1. set 함수는 리렌더링 동일성이 보장된다. 
   함수형 컴포넌트가 새롭게 렌더링된다고 하여도, set 함수는 이전의 set 함수와 같고, 데이터를 업데이트하는 경우 반영이 된다는 것이다. 
   즉, 과거의 렌더링에 있던 set함수 호출은 state를 바꾸고 리렌더링이 일어난다. 
2. 비동기 요청은 순서를 보장하지 않는다. 


## Clean up 적용하기 

위와 같이 race condition이 발생하고, 먼저 요청한 request가 이후에 들어온다면 데이터가 맞지 않는 경우가 생긴다.

이러한 경우 `clean up`을 활용하여, 먼저 요청한 request가 후에 들어오는 경우 상태값을 변경하지 못하도록 할 수 있다. 

만약 {id : 10 } -> { id : 20 } 으로 prop이 바뀌게 된다면 아래와 같은 순서를 띄게 될 것이다. 

1. {id : 20}으로 렌더링이 일어난다. 
2. {id: 10}인 effect를 cleanup 한다. 
3. { id: 20} 으로 effect를 수행한다.

아래의 코드는 2번 단계에서 즉, cleanup에 `isCancelled` boolean 값을 통해서 true로 바꿔주는 경우, 
첫번째 id = 10 인 유저의 데이터는 요청이 들어온다고 하여도 이를 상태값으로 업데이트하지 않도록 해준다. 

```javascript
const UserProfile = ({ id }) => { 
    useEffect(() => {
        let isCancelled = false; 
        ( async () =>{ 
            const userProfile = await get(url, id);
        
            if(!isCancelled) {
                setProfile(userProfile);
            }
        });
        return () => {
            isCancelled = true;
        };
    }, [id]);
    
    return (<div> {id} <div>); 
};
```


참고: https://www.robinwieruch.de/react-hooks-fetch-data
## Suspense
