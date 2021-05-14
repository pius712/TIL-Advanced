# 객체
## 메서드
엄밀히 말해서 javascript의 메서드는 일반 객체지향 언어에서의 메서드와는 다르다.

왜냐하면, 특정 객체의 메서드는 특정 함수에 대한 레퍼런스를 가질 뿐 실제로 그 메서드를 객체가 가지는 것이 아니기 때문이다.

```javascript
function foo() {
	console.log('bar');
}
var someFoo = foo;

var obj = {
	someFoo: foo
};

obj.someFoo === foo 
>>> true
obj.someFoo === someFoo
>>> true
```

obj.someFoo, someFoo 모두 같은 함수에 대한 레퍼런스를 가지고 있을뿐이다.


### 객체의 프로퍼티

객체의 프로퍼티는 실제로는 `key - value` 쌍만을 가지고 있는 데이터가 아니다.
내부적으로 객체는 `flag`를 가지고 있고, 아래의 세가지 속성을 boolean 값으로 가진다.

- writable : 쓰기 전용인가?
- enumerable : 열거가 가능한가?
- configurable : 프로퍼티의 삭제나 플래그의 수정이 가능한가?

-> 일반적으로 객체를 생성하고, 프로퍼티를 만들면 위의 flag 모두가 true가 된다.


Ownership of properties (속성의 소유권)이란 무엇인가?


## Object 클래스

자바스크립트의 거의 모든 객체는 `Object class`의 인스턴스이다.

- Object.create(proto);

proto 객체로 인스턴스를 만들어준다.

예제 1 에서 `person` 객체가 prototype이 된다.
즉, `me.__proto__ === person`이 된다.

```javascript
// 예제 1
const person = {
	name = '',
	sayHello = function () {
		console.log("my name is `${this.name}`");
	} 
};

const me = Object.create(person);
me.name = 'pius';
me.sayHello();
```


예제 2
예제 2에서는 Person 이라는 생성자 함수를 사용하였다.  
Person.prototype을 proto로 하는 p1 객체는, `new` operation을 통해 만든 p2객체와 마찬가지로 `Person.prototype`을 상속한다.

```javascript
// 예제 2
function Person(name) {
	this.name = name;
	this.sayHello = function () {
		console.log("my name is `${this.name}`");
	}
}
const p1 = Object.create(Person.prototype);
const p2 = new Person('pius');
```

- Object.getPrototypeOf(obj)

obj 객체의 프로토타입을 반환해주는 함수.

```javascript
function Person(name) {
	this.name = name;
	this.sayHello = function () {
		console.log("my name is `${this.name}`");
	}
}
const p1 = new Person('pius');

Object.getPrototypeOf(p1) === p1.__proto__;
>>> true
```

- Object.keys(obj);

obj 객체의  열거가능한(enumerable) 프로퍼티들을 배열로 반환해준다.



- Object.getOwnPropertyNames(obj);

obj의 모든 프로퍼티 (non-enumerable property 포함) 를 배열로 반환해준다.

여기서 열거 불가능한 프로퍼티랑 무엇인가?



-





























#javascript/객체