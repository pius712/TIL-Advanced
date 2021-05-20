# useRef

# hooks


## useRef( ...[value])

cf) 초기 값을 넣어주면, ref.current = value가 실행된다.


실제 DOM에 접근해야하는 경우가 생긴다.
ref를 통해서 DOM에 접근할 수 있다.


```js
const*inputRef*=*useRef*();

*useEffect*(()=> {
  *inputRef.current.focus*();
},[])
*return* (
  <div>
    <input *ref*={inputRef}>
  </div>
)
```

ref는 html element에 붙일 수 있고,
클래스 컴포넌트에 붙일 수 있다.
함수형 컴포넌트에서는 아무런 설정을 하지 않는다면, ref 속성값을 붙일 수는 없다.

`forwardRef`
```js

*export* *default* function *App*() {

  *return*(

    <div>
      <Button *ref*={buttonRef}/>
    </div>
  )
}

const*Button*=*React.forwardRe*(function({*onClick*})*, ref)*{
*return*(
<button *onClick*={onClick} *ref*={ref}>
      save
    </button>
)
}
```

아래와 같이 ref에 함수를 작성하면, 해당 컴포넌트가 생성될때마다 생성된다.
아래의 경우에는 button을 클릭하면 ref가

컴포넌트가 렌더 될 때마다, ref안에 함수를 실행하게 되는데(왜냐하면, 메모이제이션 되지 않은 함수이기 때문이다.)
아래의 로직은 input 내부에 글자를 작성하면, text가 바뀌게 되어서 input을 다시 렌더링하게 되는데,
ref 내부의 함수가 실행되면서 setText(INITIAL_TEXT)가 실행이 된다.



```js
*export* *default* function *App*() {
  const[*text*,*setText*]=*useState*();
  const[*showText*,*setShowText*]=*useState*();
  
  *return* (
    <div> 
      {showText && (
        <input 
          *type*="text"
          *ref*={ref=> ref && *setText*(INITIAL_TEXT)}
          *value*={text}
          *onChange*={e=> *setText*(*e.target.*value)}
          />
      )}
      <button *onClick*= {() => *setShowText*(!showText)}>보이기/가리기</button>
  )
}
```
그렇기 때문에, useCallback 훅을 사용하면, 이 함수를 메모이제이션 하기 때문에,
함수를 다시 실행하지 않는다.


```js
*export* *default* function *App*() {
  const[*text*,*setText*]=*useState*();
  const[*showText*,*setShowText*]=*useState*();
  
  const*setInitialText*=*useCallback*(*ref*=>ref&&*setText*(*INTIAL_TEXT*),[]);
  *return* (
    <div> 
      {showText && (
        <input 
          *type*="text"
          *ref*={setInitialText}
          *value*={text}
          *onChange*={e=> *setText*(*e.target.*value)}
          />
      )}
      <button *onClick*= {() => *setShowText*(!showText)}>보이기/가리기</button>
  )
}
```

동적으로 생성되는 요소들에 ref를 달아주는 방법?
hook의 경우에는 반복문, 제어문에서 실행될 수 없기 때문에, 동적으로 만들어 줄 수 없다.

그렇기 때문에, ref

```js
*export* *default* function *Component*({list}) {

  const*listRef*=*useRef*({});

  function *onClick*() {
    letmaxRight=0;
    letmaxId='';
    
    *for* (const*item*of list){
      const*ref*=*list.current*[*item.id*];
      if(ref){
        const*rect*=*ref.getBoundingClientRect*();
        if(maxRight < *rect.*right){
          maxRight = *rect.*right;
          maxId = box,id;
        }
      }
    }
    *alert*(`제일 끝 요소는 ${maxId}`)
  }
  const*setInitialText*=*useCallback*(*ref*=>ref&&*setText*(*INTIAL_TEXT*),[]);
  *return* (
    
    <>
      <div>
        {
          *list.map*(item => (
            <div
              *ref* = {ref=> *list.*current[*item.*id] = ref}>
              {item}
            </div>
          ))
        }
      <div>
      <button *onClick*={onClick}>
    </>
  )
}
```



## useCallback

## useMemo

## useReducer

## use

#React/hooks/useRef

