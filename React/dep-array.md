# Dependency Array


## deps에 거짓말하지 마라


 아래의 경우 count는 0 -> 1로 한번 증가한 후 더 이상 증가하지 않는다. 
왜냐하면, 클로저로 인해서 `count`는 처음에 렌더링 당시의 값 (`0`)을 보고 있기 때문이다. 

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return <h1>{count}</h1>;
}
```

### 해결 방법1. deps를 솔직하게 

 아래의 코드의 경우에는 count가 바뀔 때마다 effect 함수가 갱신되기 때문에, 매 초마다 카운트가 상승하게 된다. 
하지만, effect의 특성상, 리렌더링시에 cleanup 코드가 실행되고 다시 effect가 실행되기 때문에, interval이 설정과 해제를 
매초마다 반복하게 된다. 

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, [count]);
  return <h1>{count}</h1>;
}
```

### 해결 방법 2. 의존성 제거(콜백 사용하기)

 effect 함수 자체를 수정해서, 의존성 자체를 제거해버리는 방법도 있다. 
`setCount` 함수에서 `count` 상태를 사용하지 않고, 콜백으로 처리해버리면 이를 해결할 수 있다. 

실제로 카운트를 올리기 위해서 count 값 자체가 필요하기 보다는 `이전의 값 + 1`이 필요한 것이기 때문에, 
의존성을 제거할 수 있었던 것이다. 

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((prevCount) => prevCcount + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return <h1>{count}</h1>;
}
```

### useReducer 사용

 2번의 문제를 사용하는 것은 단일 상태 값에 상수를 가지고 연산할 때는 문제가 없는데, 
만약 다른 상태 값을 추가하여 연산하게 되는 경우에, deps array에 추가하는게 불가피해진다. 

아래의 경우 step을 가지고 연산을 하게 되어, step을 deps에 추가하였고, 이로인해서 매 렌더링마다 
interval의 구독과 해지가 일어나게된다. 
```javascript
function Counter() {
  const [count, setCount] = useState(0);
  count [step, setStep] = useState(1);
  useEffect(() => {
    const id = setInterval(() => {
      setCount((prevCount) => prevCcount + step);
    }, 1000);
    return () => clearInterval(id);
  }, [step]);
  return (
        <>
            <h1>{count}</h1>
            <input onChange={(e)=> setStep(e.target.value)}/>
        </>
  );
}
```

이를 해결하기 위해 useReducer를 사용할 수 있다.

dispatch 함수의 경우, 렌더링과 관계없이 함수가 동일하다는 것을 보장하기 때문이다. 


```javascript
function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  useEffect(() => {
    const id = setInterval(() => {
      dispatch({
          type: 'tick'
      })
    }, 1000);
    return () => clearInterval(id);
  }, [step]);
  return (
        <>
          <h1>{count}</h1>
          <input onChange={(e)=> setStep(e.target.value)}/>
        </>
  );
}

const initialState = {
    count: 0,
    step: 1,
}
function reducer(state, action) {
    const {count, step } = state;
    if(action.type === 'tick'){
        return {
            count: count + step,
            step,
        }
    }else if( action.type === 'step') {
        return {
            count,
            step : action.payload.step,
        }
    }else {
        throw Error();
    }
    
}
```


## Effect 훅 내에서 함수 호출

 Effect 훅 내부에서 함수를 호출하는 경우에, 클로저로 인해서 문제가 발생할 수 있다. 

아래의 경우 `useEffect` 훅의 deps를 빈 배열로 설정하여서, 
컴포넌트가 mount된 이후 수행되지 않는다.

하지만 해당 컴포넌트가, 예를들어, 사용자의 id, 닉네임에 따라 변경되야 할 수도 있다. 
즉, state, prop에 의해 동기화되어야 할 필요가 있을 수도 있다.
이 경우에는 useEffect에 deps를 추가해야 하는데, 여러 함수를 호출하는 경우에, deps를 실수할 가능성이 생긴다. 

```javascript
function Example({ someProp }) {
    
    function doSomething1(state) {
        console.log(someState);
    }
    function doSomething2() {
        console.log(someProp);
        doSomething1();
    }
    
    useEffect(() => {
        doSomething();
    }, []); 
    // 🔴 이것은 안전하지 않습니다 (`someProp`을 사용하는`doSomething`을 호출합니다)
}
```

### 내부로 옮기기

위의 코드와 달리 effect 훅 내에서 함수를 선언하고 사용하게 되는 경우,
effect 훅 내부에서 사용되고 있는 함수들을 이행적으로 찾아가며, deps를 추가할 필요가 없어진다.
이로 인해서 만약 state나 props를 사용하게 된다고 하더라도, deps를 추가하는데 있어서 실수할 확률이 줄어든다. 


```javascript
function Example({ someProp }) {
    useEffect(() => {
        function doSomething1(value) {
            console.log(value);
        }
        function doSomething2() {
            console.log(someProp);
            doSomething1(someProp);
        }
    }, [someProp]); 
}
```

### 내부로 옮기지 못하는 상황

 함수가 여러군데서 사용되어, 이를 내부로 옮기는 경우에 코드가 중복되는 상황이 있을 수 있다.
이런 경우에는 effect 훅 내부로 함수를 옮기는 것이 달갑지 않다. 

아래의 코드는 `getFetchUrl` 함수를 사용함에도 의존성 배열에 추가하지 않은 상황이다. 
이 경우 effect의 의존성에 거짓말을 하고 있는 것이다. 

물론, 실제 동작에는 문제가 없다. `getFetchUrl` 함수의 경우, 매 렌더링마다 새로운 함수로 만들어지지만,
그 함수의 내용이 변하지도 않고, effect 훅내에서 사용되는 함수 인자도 상수이기 때문이다.


```javascript
function SearchResults() {
  function getFetchUrl(query) {
    return 'https://hn.algolia.com/api/v1/search?query=' + query;
  }

  useEffect(() => {
    const url = getFetchUrl('react');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, []); // 🔴 빠진 dep: getFetchUrl

  useEffect(() => {
    const url = getFetchUrl('redux');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, []); // 🔴 빠진 dep: getFetchUrl

  // ...
}
```

하지만 의존성 배열에 추가하지 않는 경우, 이 후에 effect에
어떤 state, props를 추가하게 되어 데이터의 흐름에 변화를 주게 되는 경우에,  
이를 알아차리기 어렵게 만든다.

아래의 경우 `getFetchUrl` 함수에 쿼리스트링에 props로 넘어온 userId를 추가된다면, 
effect 훅은 userId props이 변경되어도 동기화하는데 실패할 것이다.

```javascript
function SearchResults({ userId}) {
  function getFetchUrl(query) {
    return `https://hn.algolia.com/api/v1/search?query=${query}?id=${userId}`;
  }

  useEffect(() => {
    const url = getFetchUrl('react');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, []); 

  useEffect(() => {
    const url = getFetchUrl('redux');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, []);
  // ...
}
```

그렇다고 반대로 의존성 배열에 `getFetchUrl`을 추가한다면? 

아래의 코드는 매 렌더링 마다 해당 함수를 생성하기 때문에, state나 props의 변화에 따라 항상 
effect 훅을 호출하게 된다. 

```javascript
function SearchResults() {
  function getFetchUrl(query) {
    return 'https://hn.algolia.com/api/v1/search?query=' + query;
  }

  useEffect(() => {
    const url = getFetchUrl('react');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, [getFetchUrl]); 

  useEffect(() => {
    const url = getFetchUrl('redux');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, [getFetchUrl]); 

  // ...
}
```

### useCallback 사용하기

아래와 같이 사용되는 함수를 useCallback 함수를 통해 정의할 수 있다. 
이렇게 되면, 매 함수 렌더링 마다 getFetchUrl을 다시 생성하지 않을 것이고, 
따라서, effect에 getFetchUrl을 의존성 배열에 추가한다고 하여도 매 렌더마다 실행되지 않을 것이다.

```javascript
function SearchResults() {
    const getFetchUrl = useCallback((query) => {
        return 'https://hn.algolia.com/api/v1/search?query=' + query;
    }, []);

  useEffect(() => {
    const url = getFetchUrl('react');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, [getFetchUrl]); 

  useEffect(() => {
    const url = getFetchUrl('redux');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, [getFetchUrl]); 

  // ...
}
```

만약 state, props를 getFetchUrl에서 의존하게 된다면, 
이를 알아차리는 것도 쉽고, effect도 그에 따라 동기화가 될 것이다. 

아래의 경우 쿼리 스트링에 id 가 추가되었고, props의 id를 추가후 의존성 배열에 추가한 코드이다. 

```javascript
function SearchResults({userId}) {
    const getFetchUrl = useCallback((query) => {
        return `https://hn.algolia.com/api/v1/search?query=${query}?id=${userId}`;
    }, [userId]);

  useEffect(() => {
    const url = getFetchUrl('react');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, [getFetchUrl]); 

  useEffect(() => {
    const url = getFetchUrl('redux');
    // ... 데이터를 불러와서 무언가를 한다 ...
  }, [getFetchUrl]); 

  // ...
}
```
