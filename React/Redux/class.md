#  리덕스 레거시
# 액션

## redux-actions

redux-actions 라이브러리는 action string을 유지하는 번거러운 작업을 줄이기 위해서 만들어졌다.  
handling, creating actions 를 위한 helper로 Flux Standard Action(FSA)를 보다 쉽게 만들 수 있다.

```js
*import* {createActon, handleActions} *from* 'redux-actions'
```

### createAction

`createAction(type)` 은 action creator를 반환한다.

```js
*export*const*increment*=*createAction*('INCREMENT');
*export*const*decrement*=*createAction*('DECREMENT');

*increment*(); //*{ type: 'INCREMENT' }*
*decrement*(); //*{ type: 'DECREMENT' }*
*increment*(10); //*{ type: 'INCREMENT', payload: 10 }*
*decrement*([1, 42]); //*{ type: 'DECREMENT', payload: [1, 42] }*
```

### handleAction

`handleAction(type, reducer, defaultState)`

리듀서 함수가 전달되면,
If a reducer function is passed, it is used to handle both normal actions and failed actions. (A failed action is analogous to a rejected promise.) You can use this form if you know a certain type of action will never fail, like the increment example above.
If the reducer argument (reducer) is undefined, then the identity function is used.
The third parameter defaultState is required, and is used when undefined is passed to the reducer.
3
```js
*import* { handleAction } *from* 'redux-actions';

*handleAction*(
  'APP/COUNTER/INCREMENT',
  (state, action) => ({
    counter: *state.*counter + *action.payload.*amount
  }),
  defaultState
);
```

### handleActions

`handleActions(reducer, defaultState);`

리듀서 여러개를 만들어서 이를 하나의 리듀서로 합치게 해준다. 내부적으로, 이 함수는 `reduce-reducers` 라는 라이브러리를 사용해서 차례대로 리듀서를 적용한다.

첫번째 파라미터인 리듀서는 key(action type) : value(reducer) 형식이다 .

두번째 파라미터 defaultState는 reducer로 `undefined` 값이 전달되는 경우에 사용되는 값이다.

```js
const*reducer*=*handleActions*(
{
*INCREMENT*:(*state*,*action*)=>({
*counter*:*state.counter*+*action.payload*
}),

*DECREMENT*:(*state*,*action*)=>({
*counter*:*state.counter*-*action.payload*
})
},
{*counter*:0}
);
```

## 비교


### 리덕스 툴킷

리덕스 툴킷에는 `slice`라는 단위가 있다. 이 `slice`는 하나의 feature를 위해 만들어지는 파일에 리듀서 로직과 액션들의 컬렉션이다.
```js
features/counter/*counterSlice.*js
*import* { createSlice } *from* '@reduxjs/toolkit'

*export*const*counterSlice*=*createSlice*({
*name*:'counter',
*initialState*:{
*value*:0
},
*reducers*:{
*increment*:*state*=>{
      //*리덕스 툴킷에서는 리듀서 내부에서 "mutating" 로직으로 작성을 해도 된다. Redux Toolkit allows us to write "mutating" logic in reducers. It*
      //*실제로 state 값을 mutate 하는 것은 아니고 내부적으로 immer lib를 쓰기 때문이다.*
*state.value*+=1
},
*decrement*:*state*=>{
*state.value*-=1
},
*incrementByAmount*:(*state*,*action*)=>{
*state.value*+=*action.payload*
}
}
})

*export*const{*increment*,*decrement*,*incrementByAmount*}=*counterSlice.actions*
//*이 export를 통해서 다른 컴포넌트에서 해당 action을 호출 해준다.*
*export* *default* *counterSlice.*reducer
//*이 export를 통해서 createStore에서 리듀서를 만들어준다.*
```
위와 같이 createSlice() 함수를
"name option" + "reducer key name"이 자동으로 생성된다.

그렇기 때문에, 해야할 것은 slice의 이름을 정하고, reducer 함수들이 들어있는 객체를 작성하는 것이다. 그러면 그에 대한 action 코드를 자동으로 생성해준다. name option에서의 string은 action type의 첫번째 파트로 사용된다. 그리고 각 리듀서 함수의 키 이름이 두번째 파트가 된다.
따라서 "counter" + "increment" 리듀서 함수는 {type: "counter/increment"} 액션 타입을 만들어 낸다.




#레거시/리덕스