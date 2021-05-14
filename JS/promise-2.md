# Promise2
```javascript

const p1 = new Promise((res, rej) => {});
// 호출 당시에는 pending
// 이후에 res -> fulfilled
// rej -> rejected
const p2 = Promise.reject('error message');
cosnt p3 = Promise.resolve(param);

// 대기중 (pending)
// 성공 fulfilled
// 실패 rejected

// 성공 || 실패 settled
```


```javascript
requestData().then(onResolve, onReject);
// requestData 함수 호출 후
// 성공시 (fulfilled) onResolve 호출
// 실패시 (rejected) onReject 호출

Promise.resolve(123).then(data => console.log(data));
Promise.reject('error').then(null, data => console.log(data));

```


then 메서드의 리턴 값에 따라서 다르게 동작을 하지만, 항상 Promise 객체를 반환한다.

- 값을 반환하는 경우: 그 값으로 하는  fulfilled Promise 반환
- 값을 반환하지 않는 경우 : undefined를 결과값으로 하는 fulfilled Promise 반환
- 오류를 반환하는 경우:  해당 오류를 값으로 하는 rejected Promise 반환


```javascript
function requestData1() {
	return new Promise((resolve, rejecte) =>{ 
		setTimeout(()=>{ 
			resolve(10);
		}, 1000);
	});
}
function requestData2() {
	return new Promise((resolve, rejecte) =>{ 
		setTimeout(()=>{ 
			resolve(20);
		}, 1000);
	});
}
requestData1()
	.then( data => {
		console.log(data);
		return requestData2();
	})
	.then( data => {
		console.log(data);
		return data + 1;
	})
	.then( data => {
		console.log(data);
		throw new Error('some error');
	})
	.then(null, error => {
		console.log('some error');
	})
	.then( data => {
		console.log(data);
	})
// 10
// 20
// 21
// some error
// undefined

```


```javascript
Promise.reject('err')
	.then(() => console.log('then 1'))
	.then(() => console.log('then 2'))
	.then(
		() => console.log('then 3'),
		() => console.log('then 4')
	)
	.then(
		() => console.log('then 5'),
		() => console.log('then 6')
	)
// then 4
// then 5
```


## Catch 메서드
```javascript
// 위 아래는 같음
Promise.reject(1).then(null, error =>> {
	console.log(error);
});
Promise.reject(1).catch(error =>{ 
	console.log(error);
});
```


이 경우에 1번째 인자에서 발생한 에러는 두번째 인자의 error가 잡아주지 못하기 때문에, 따로 처리를 해줘야한다.
```javascript
Promise.resolve().then(
	() => {
		throw new Error('some error');
	},
	error => {
		console.log(error);
	}
);

// 이러한 방식으로 추가하는 것이 좋다.
Promise.resolve()
.then(() => {
		throw new Error('some error');
	})
	.catch(error => {
		console.log(error);
	}
);

```


```javascript
Promise.reject(10)
.then(() => {
		console.log('then1:', data);
		return 10;
	})
	.catch(error => {
		console.log('catch:', data);
		return 30;
	})
	.then(data => {
		console.log('then2:', data);
	});
// catch: 10
// then2: 30
```

## Finally 메서드

Finally 메서드는 fulfilled, rejected 상태 모두 처리가 가능.

Finally는 데이터가 넘어오지도 않고, 그 전에 있던 반환값을 그대로 반환한다.
```javascript
Promise.resolve(10)
	.then((data) => {
		console.log('onThen:', data);
		return data + 1;
	})
	.catch(error => {
		console.log('onCatch');
		return 100;
	})
	.finally(() => {
		console.log('onFinally');
	})
	.then(data => {
		console.log('onThen:', data);
		return data + 1;
	});
// onThen: 10
// onFinally
// onThen: 11
```

```javascript
function requestData() {
	return fetch()
		.catch(error => {
			// ...
		})
		.finally(() =>{ 
			sendLogToServer('requestData finished');
		});
}

requestData().then(data => console.log(data));
```



## 병렬 처리
체이닝을 하게되면, 병렬로 처리되지 않는다.
1초가 걸리는 request를 2개를 보낸다고 생각하면, 총 2초가 걸리게된다.

하지만 두 함수간의 의존성이 없다면 병렬로 처리하는 것이 더 좋은 선택일 수도 있다.

```javascript
function requestData1() {
	return new Promise((resolve, rejecte) =>{ 
		setTimeout(()=>{ 
			resolve(10);
		}, 1000);
	});
}
function requestData2() {
	return new Promise((resolve, rejecte) =>{ 
		setTimeout(()=>{ 
			resolve(20);
		}, 1000);
	});
}

// 체이닝 방식
requestData1()
	.then(data => {
		console.log(data);
		return requestData2();
	})
	.then(data =>{ 
		console.log(data);
	});
// 10 (1초후)
// 20 (2초후)

// 병렬처리
requestData1().then(data => console.log(data));
requestData2().then(data => console.log(data));
```

### Promise.all

인자로 배열에 Promise 객체를 입력.
Promise.all 함수 Promise 객체를 반환하기 때문에, then 메서드를 수행할 수 있다.

이 경우 Promise.all 의 인자로 전달한 배열의 모든 Promise가 fulfilled 상태가 되어야 then 메서드가 수행된다.

하나라도 rejected 상태라면 메서드의 반환 값은 rejected이다.
```javascript
Promise.all([requestData(), requestData2()])
	.then(([data1, data2]) =>{ 
		console.log(data1, data2);
	});
```


### Promise.race

인자로 전달된 Promise 객체중에 하나라도 settled 상태가 되면 settled된 프로미스가 반환이 된다.

```javascript
Promise.rece([requestData(), requestData2()])
	.then( data => console.log('fulfilled', data))
	.catch(error => console.log('rejected'); 
```


Promise 객체가 settled 상태가 되면, 그 상태도 변하지 않고 데이터도 변하지 않는다.
```javascript
let cachedPromise;
function getData() {
	cachedPromise = cachedPromise || requestData();
	return cacehdPromise;
}
getData().then(v => console.log(v));
// 첫 호출때는 requestData()가 수행이 된다.
getData().then(v => console.log(v));
// 두번째 호출 시에는 cachedPromise가 사용된다.
```


then 메서드는 기존의 프로미스 객체를 수정하지 않는다.


```javascript
function requestData() {
	const p = Promise.resolve(10);
	p.then(data => {
		return data + 20;
	});
	return p;
}
requestData().then(v => {
	console.log(v);
});
// 이렇게 작성하면 10이 찍히게 된다. 
// 왜냐하면 p 객체는 처음에 호출한 Promise.resolve(10);이라서
// 데이터는 10이다.

// ================ //

function requestData() {
	const p = Promise.resolve(10);
	return p.then(data => {
		return data + 20;
	});
}
requestData().then(v => {
	console.log(v);
});
// 30
```

프로미스 중첩시, 데이터 넘기는 방법

```javascript
requestData1()
	.then(result1 =>{ 
		return Promise.all([result1, requestData2(result1)]);
	})
	.then(([result1, result2]) => {
		//...
	});
```

동기 코드와 사용

동기 함수에서 예외가 발생하면 catch 함수에서 잡아주지 못한다.

이 동기 함수가 fetch 전에 처리되어야 하는 것이 아니라면,  then 메서드 안으로 넣어주면, 동기 코드에서 발생하는 에러를 처리해줄 수 있다.

fetch 전에 처리되어야 하는 함수라면, Promise 객체를 만들어서 처리해줄 수도 있다.
```javascript
function doSync(){
	throw new Error('some error');
}
function requestData() {
	doSync();
	return fetch()
		.then(data => console.log(data));
		.catch(error => console.log(error));
}

function requestData() {
	return fetch()
		.then(data => {
			doSync();
			console.log(data));
		})
		.catch(error => console.log(error));
}

function requestData() {
	return Promise.resolve()
		.then(doSync)
		.then(fetch)
		.then(data => {
			doSync();
			console.log(data));
		})
		.catch(error => console.log(error));
}

```

#javascript/비동기함수