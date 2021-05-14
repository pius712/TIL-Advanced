# 리덕스 툴킷

## 스토어 생성 및 주입
```ts
import { configureStore } from '@reduxjs/toolkit';
import userReducer from './reducerA';
import postReducer from './reducerB';

const reducer = { 
	user : userReducer,
	post:  postReducer,
}
 
// 스토어 생성 및 미들웨어 설정
export default configureStore({ 
	reducer,
	middleware : (getDefaultMiddleware) =>
		getDefaultMiddleware()
  			.concat(logger)
			.concat(otherMiddleware),
}); 
```

```javascript
import store from 'store/index.js';
import { Provider } from 'react-redux';
// 스토어 주입
ReactDOM.render(
		<Provider store={store} >
			<App />
		</Provider>
	),
	document.getElementById('root');
)
```


## Slice
redux toolkit에서 slice는 리듀서와 action creator 등의 기능들을 제공해주는 객체이다.

```javascript
import { createSlice } from '@reduxjs/toolkit';

export const counterSlice = createSlice({
	name: 'counter',
	initialState: {
		value: 0,
	},
	reducers : {
		increment: state =>{ 
			state.value += 1;
		},
		incrementByAmount: (state, action) =>{
			state.value += action,payload;
		}
	}
})

export const { increment, incrementByAmount }
 = counterSlice.actions;
export default counterSlice.reducer;

export const incrementAsync = createAsyncThunk('', 
	async () => {
		const response = await fakeAPI.get('/');
		return response.amount;
	}); 
```

## Action

Action은 일반 plain object인데, 이 액션을 디스패치하기 위해서는  아래를 모두 작성해주어야 했다.

-  액션 type string
- 액션 객체
- 액션 생성자 함수

`createSlice()` 함수는 `reducers` 필드에서 작성해준 리듀서 함수와 같은 이름의 액션 생성자 함수를 만들어준다.


```javascript
// export const {increment, incrementByAmount }
//  	= counterSlice.actions;

import { increment } from './counter'; 

dispatch(increment());
```


## CreateAsyncThunk
해당 함수를 사용하면, 아래와 같은 액션을 별도로 생성할 필요가 없이, 자동으로 만들어준다.


- start
- success
- failure

리덕스 툴킷의 도움없이 작성된 비동기 함수의 예.

- start / sucuess / failure 에 대한 각각의 액션 생성자 함수.
- thunk 함수

```javascript
// 이전의 thunk 함수 예
const fetchStart = () => {
	type: 'post/start'
}
const fetchSuccess = () => {
	type: 'post/start'
}
const fetchFailure = () => {
	type: 'post/start'
}
const fetchPosts = (id) => async dispatch => {
	dispatch(fetchStart())
	try {
		const res = await exampleAPI.get('/');
		dispatch(fetchSuccess());
	} catch(err) {
		dispatch(fetchFailure());
	}	
}
```

리덕스 툴킷을 적용하면?

`createAsyncThunk` 함수를 사용하면, 자동으로

- '1st args'/pending, fulfilled, rejected가 나온다.
  즉,  `posts/fetchPosts/pending`과 같은 방식으로 자동으로 action type string이 생성된다.



```js
export const fetchPosts = createAsyncThunk('posts/fetchPosts', 
	async () =>{ 
		const res = await exmpleAPI.get('/');
		return res.data;
	}
);
```


하지만, createSlice 함수로 만들어진 객체는 결국에, reducers에 정의되어 있는 함수들에 대한 액션 디스패치에 응답하게 된다.

`createAsyncThunk` 함수의 경우에는 리듀서가 아니라 별개의 영영에 함수를 정의하여서 export 한 것이기 때문에, 이를 받아줄 별도의 영역이 필요한데 이것이 바로 `extraReducers` 이다.


```javascript
const postReducers = createSlice({
	name: 'post',
	initialState,
	reducers : {
		// ...
	},
	extraReucers: {
	  [fetchPost.pending]: (state, action ) =>{ {
		// ...
	  },
    [fetchPost.fulfilled]: (state, action ) =>{ {
		// ...
	  },
    [fetchPost.rejected]: (state, action ) =>{ {
		// ...
	  },
	} 
})
```




















#리덕스/리덕스툴킷# #Cheat-Sheet
