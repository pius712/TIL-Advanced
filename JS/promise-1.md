#  Promise

## Promise
프로미스는 결국에 비동기 함수를 일반 동기 함수와 같은 방식으로 사용하기 위해서 고안된 방식이다. 기존에는 비동기 함수를 처리하기 위해서는 콜백 함수를 사용해야 했는데, 이러한 방식은 콜백 헬과 같은 코드를 만들 수 있기 때문이었다.

그렇다면 이를 비동기 함수를 동기 함수와 같은 방식으로 처리하기 위한 해결책은 무엇일까?  함수와  값 사이에 프록시를 두는 것이다. 그것이 바로 프로미스이다 .

프록시를 통해서 함수의 결과 값에 대한 정보를 얻도록 하는 것이다.

Promise 객체는 3개 중에 하나의 state를 가진다.

- pending
- fulfilled
- rejected

pending 상태의 프로미스는 이후에 settled (fulfilled / rejected) 상태로 이행될 수 있다.

착각하지 말아야할 것이 resolved !== fulfilled 이다.  `resolved`의 의미는 프로미스가 settled 되었다는 뜻이다.


```js
let promise = new Promise(function(resolve, reject) {
	resolve();
	reject();
});

promise.then(
	function(value) { }
	function(error) { }
);
```

비동기 함수를 처리한 다음에 불러온 데이터가 필요한데, 이를 어떻게 처리할 것이냐?
당장에 함수를 호출하는 시점에서는 (내부에 비동기 함수가 있다면) 그 값에 대한 정보는 `pending` 상태일 것이다 .

이 받아온 데이터에 대한 핸들러 함수를 이 곳에 `then()` 함수에 붙여두면 프로미스가 성공적일 때, 핸들러가 실행이 된다.

반면에, 프로미스가 실패할 경우에는 `catch()` 함수가 실행되거나, `then(..., onRejection)`  then함수의 두번 째 인자가 실행이 된다.


```js
const promise = new Promise((resolve, rejection) => { 
	});

promise
	.then(()=>{ 
		console.log("success"); 
	}).catch(()=> {
		console.log("failed");
	});
```



```js
const promiseAjax = (method, url, payload) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open(method, url);
    xhr.setRequestHeader('Content-type', 'application/json');
    xhr.send(JSON.stringify(payload));

    xhr.onreadystatechange = function () {
      // 서버 응답 완료가 아니면 무시
      if (xhr.readyState !== XMLHttpRequest.DONE) return;

      if (xhr.status >= 200 && xhr.status < 400) {
        // resolve 메소드를 호출하면서 처리 결과를 전달
        resolve(xhr.response); // Success!
      } else {
        // reject 메소드를 호출하면서 에러 메시지를 전달
        reject(new Error(xhr.status)); // Failed...
      }
    };
  });
};
```




### 프로미스 체인

프로미스 체인은 특정 settled 프로미스에 `then()` `catch()` `finally()` 와 같은 메서드들을 붙여서 실행하는 것이다.


#javascript/비동기함수
 