# JS 코어
- Data Type
- 함수
- 실행 컨텍스트
- 클로저
- this
- 프로토타입
- 클래스

## Data Type
- **Primitive Type** - number , string , boolean, null, undefined

값을 그대로 할당.

```javascript
var a = 123;
// 위 과정은 아래와 같은 과정을 거치게 된다.
var a; // 변수 선언, 메모리에 변수를 생성한다.
a = 123; // 할당, (메모리에 임의의 주소)에 123이라는 값을 할당하고 
		   // 임의의 주소를 가리키게 된다.
```


- **Reference Type** - Object ( array, function, RegExp,...)

값의 참조형을 할당.
```javascript
var obj = {
	a: 1,
	b: 'hello',
};

// 위 과정은 아래와 같은 과정을 거치게 된다.
var obj; 
// 변수 선언, 메모리에 변수 obj를 저장한다. 
obj = {
	a: 1,
	b: 'hello',
};; 
// 할당을 할때, 객체는 레퍼런스 타입이기 때문에,
// 객체를 담는 메모리 공간을 생성한 후,  
// 변수 a가 해당 객체 {} 의 메모리 주소를 가리킨다. 
// 객체의 프로퍼티들에 대한 변수를 메모리에 할당하고, 
//  값을 메모리에 임의의 주소에 만들어 할당한 뒤, 
//  프로퍼티가 이 값이 있는 메모리 주소를 가리킨다. 
```

레퍼런스 타입은 이러한 과정을 거치기 때문에,

`var obj2 = obj` 를 실행하는 경우에는 레퍼런스를 복사해준다.
이 경우에는, obj2 객체에 프로퍼티 값을 변경하면, obj 객체의 프로퍼티 값도 바뀐다.

```javascript
var obj2 = obj;
obj2.a = 2;
obj2.b = 'bye';

obj.a === 2 
>>> true
obj.b === 'bye;
>>> true
```

## 함수
### 호이스팅

```javascript
// 함수 선언문
function a() {
 //...
}
// 함수 표현식
var b = function () {

}
var c = 'hello world';

// 위의 호이스팅
function a() {
 //...
}
var b;
var c;
// 이후 b, c 에 값 할당.
```

### 함수 스코프

```javascript
var a = 1;
function outer() {
	console.log(a);
	function inner() {
		console.log(a);
		var a = 3;
	}
	inner();
	console.log(a);
}
outer();
console.log(a);
// 실행 결과
// >>> 1
// >>> undefined
// >>> 1
// >>> 1
```


```javascript
// 위 코드 해석
var a;
function outer() {
	console.log(a);
	function inner() {
		var a;		
		console.log(a);
		a = 3;
	}
	inner();
	console.log(a);
}
a = 1;
outer();
console.log(a);
```

## 실행 컨텍스트(Execution context)

- 콜 스택
  함수의 실행에 대한 정보를 스택에 담고 있는, 함수 호출을 위한 메커니즘.

콜 스택에 쌓이는 함수 실행에 대한 정보가 실행 컨텍스트(Execution Context)이다.  따라서, 콜 스택을 Execution Context Stack이라고 부르기도 한다.

프로그래밍 자체도 일종의 함수이기 때문에, 프로그램이 시작할때 Global Execution Context가 맨 아래에 깔리게 된다.


Execution Context
: 코드가 실행되기 위한 조건 / 환경에 대한 정보를 담은 `객체`.
: 런타임 evaluation에서 Identifier Resolution(식별자 해결)을 하기 위한 장치


실행 컨텍스트는 아래와 같이 구성된다.

- VariableEnvironment
    - environment Record (snapshot)
    - outer environment Reference (snapshot)
- LexicalEnvironment
    - environment Record -> 현재 문맥의 식별자 정보, {변수: 값 } 형태로 저장
    - outer Environment Reference -> 외부의 식별자 정보에 대한 참조
- ThisBinding


Lexical Environment(이하 LE로 생략)
: execution context 내에서, 사용되는 식별자 참조를 해결하기 위한 환경 정보.
: 함수가 실행되는 되기 직전의 compilation 단계 중 lexing 단계에서 생성되는 환경 정보. 해당 정보에는 크게 아래의 두가지 정보가 저장된다.

LE는 컴파일레이션 단계에서 생성되고,
실행 컨텍스트를 구성하는 요소 중 하나가 되는데 그 값 자체를 가지고 있는 것이 아니다.

~~따라서, 실행 컨텍스트가 사라진다고 하여도 LE가 사라지지는 않는다. 만약 LE가 사라진다면, 클로저가 발생하지 않는다.~~
그냥 내부에서 외부에 대한 변수를 참조하고 있으면, 외부의 실행 컨텍스트 중 참조되는 값이 콜 스택에서 사라지지 않는다.

- Environment Record
- Outer lexical environment의 참조값 (outer Environment Reference)

```javascript
function fn1() {
	function fn2() {
		// ...
		function fn3() {		
			// ...
		}
		fn3();
	}
	fn2();
}
fn1();
```

위와 같은 코드를 실행하면 콜 스택에 global -> fn1 -> fn2 -> fn3 의 실행 컨텍스트가 쌓이게 된다.

각각의 실행 컨텍스트에는 outer 실행 컨텍스트에 대한 참조를 가지고 있다.
- fn3 은 f2의 실행 컨텍스트를 참조하고,
- fn2는 fn1의 실행 컨텍스트를
- fn1 은 글로벌 실행 컨텍스트를 참조한다.



## 클로저
: Combination of function and the lexical environment within which that function was declared

함수와 그 함수가 선언될 당시의 lexical environment

A 의 실행 컨텍스트와 B의 outer environment reference 관계가 합쳐져서 생기는 것.

B의 outer Environment Reference는 A의 environment record를 참조한다.

따라서  A의 실행컨텍스트에 있는 변수를 내부 함수인 B에서 접근할 수 있는데,
이렇게 B에서 A의 변수를 참조할 때 **클로저** 현상이 생긴다.


클로저: 컨텍스트 A에서 선언한 변수 a를 참조하는 내부함수 B를 A의 외부로 전달할 경우,  A가 종료된 이후에도 a가 사라지지 않는 현상.


```javascript
function A() {
	var a = 1
	function B() {
		return ++a;
	}
	return B;
}
A();
```

### 클로저의 활용

```javascript
function a() {
	var
	return {

	}
}
var obj = a();
```

## this
아래의 코드를 돌리고 `foo.count`를 보면 결과는 어떻게 될까?
0이 된다.

```javascript
function foo(){
    this.count++;
}
foo.count = 0;

foo();
foo();

foo.count
>>> 0
```

this는 객체 자신을 가리키고, 함수도 자바스크립트에서는 객체이기 때문에 foo 함수를 가리키는 것이 아닌가?

**우선, this는 자기 자신을 가리키는 것이 아니다.**

그리고 **this는 함수 자신의 렉시컬 스코프를 가리키는 것도 아니다.**

아래의 코드는 foo 함수의 lexical scope을 가리켜서 호출되는 것이 아니라, 그저 this가 global객체를 가리키기 때문에 성공한 것이다.
```javascript
function foo(){
    var a = 2;
    this.bar();
}

function bar(){
    console.log('hi');
}
```


### this의 생성

this는 언제 생성될까? this는 **런타임 함수 호출시**에 바인딩되어 함수 호출 당시의 상황에 따라 실행 콘텍스트가 만들어진다.
호출부가 어떻게 구현되어 있는가에 따라서 this는 바인딩되는 대상이 달라지게 된다!

### This binding
우선적으로 내용을 요약하면, 아래에 우선순위에 따라 this 바인딩이 결정된다.

1. 함수가 new keyword가 붙어서 호출
2. call() or apply()로 함수 호출
3. 암시적 바인딩
4. default 바인딩

**default 바인딩**

아래의 코드는 default 바인딩의 예이다. default 바인딩이 될 경우에는 this는 전역 객체(window, strict 모드시 undefined 값)에 바인딩 된다.
```javascript
function foo(){
  console.log(this.a);
}
var a = "hello world"
foo();
>>> hello world
```

**암시적 바인딩**

암시적 바인딩의 경우에는 호출부에 객체가 있는지 확인하는 것이다. 즉, 호출부에 함수에 대한 컨텍스트 객체가 있으면 이 객체에 암시적 바인딩이 된다.
```javascript
function foo() {
  console.log(this.a);
}
var obj = {
  a: 'hello world',
  foo: foo
}
obj.foo();
>>> 'hello world'
```

암시적 바인딩을 할 때 주의할 점.
1. 바인딩 소실 아래의 코드에서 bar = boo.foo 부분을 조심해야한다. 여기서 변수 bar는 boo.foo를 가지는 것이 아니라, foo 레퍼런스를 직접 가리키기 때문에, 암시적 바인딩이 되지 않는다. 이 경우는 default binding이 적용된다.

```javascript
function foo(){
  console.log(this.a);
}
var boo = {
  a: 'hello world',
  foo: foo,
}
var bar = boo.foo;
var a = "bye"
bar();
>>> bye
```

2. 콜백함수
```javascript
function foo(){
  console.log(this.a);
}
function bar(fn){
  fn();
}
var obj = {
  a: 'hello world',
  foo: foo
}
a = "bye"
bar(obj.foo); // foo 함수의 레퍼런스를 직접 매개변수 fn이 가지게 된다.
>>> bye
```

### 명시적 바인딩
call() or apply() 함수를 통해서 함수를 특정 객체에 직접 바인딩 해주는 것이다.
```javascript
function foo(){
  console.log(this.a);
}
var obj = {
  a: 'hello world'
}
foo.call(obj);
>>> hello world
```

### new Binding

생성자 함수에 new를 붙여서 실행하게 되면, 생성된 객체에 this가 바인딩 된다.

```javascript
function foo(a){
  this.a = a;
}
var bar = new foo('hello world');
console.log(this.a);
>>> hello world
```

### arrow function

화살표 함수에서 this는 lexical scope을 가지게 된다.
리액트에서 class 컴포넌트에서 function 선언 대신에 화살표 함수를 사용하게 되었을 경우, 이를 prop으로 넘겨도 function 내부의 this는 넘겨 받은 함수의 this가 아니라, 생성된 클래스를 가리키게 된다.

그 이유는 화살표 함수에서의 this는 lexical scope에 따라 바인딩이 되는데, 클래스를 통해 객체가 생성되면서 내부의 화살표 함수가 해당 객체에 this 바인딩이 되기 때문이다.




#javascript/기본개념