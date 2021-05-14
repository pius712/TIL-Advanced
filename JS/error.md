# Error


##   Uncaught TypeError: Cannot read property of undefined

**TypeError** :  피연산자가 특정 연산자에 incompatible 하거나,  함수의 인자로 전달되는 값이  함수가 예상하는 type과 incompatible 한 경우에 TypeError가 발생.

**Cannot read property of undefined**

해당 에러 메세지는 undefined object에서 메서드나 프로퍼티를 읽을 때, 발생하는 에러.

```javscript 
const error = { }
console.log(error.response); 
>>> undefined
console.log(error.response.data);
>>> Uncaught TypeError: Cannot read property 'data' of undefined
````



### Axios Error handling

```javascript
axios.get('/user/12345')
  .catch(function (error) {
    if (error.response) {
      // The request was made and the server responded with a status code
      // that falls out of the range of 2xx
      console.log(error.response.data);
      console.log(error.response.status);
      console.log(error.response.headers);
    } else if (error.request) {
      // The request was made but no response was received
      // `error.request` is an instance of XMLHttpRequest in the browser and an instance of
      // http.ClientRequest in node.js
      console.log(error.request);
    } else {
      // Something happened in setting up the request that triggered an Error
      console.log('Error', error.message);
    }
    console.log(error.config);
  });

```
#javascript