# Class
- 클래스 기본
- 클래스 원리
- getter / setter
- 상속
- Class field
- static
- 접근 제어

## 클래스 기본

클래스를 통해 생성되는 메서드와 인스턴스 변수.

객체의 메서드는 클래스의 prototype property를 통해 상속받는 메서드이다. 
이것도 정확하게는 클래스의 prototype property의 메서드를 복사하는 것이 아니라, 
해당 메서드의 참조값을 받아오는 것이라서 다른 클래스 지향언어와는 일치하지는 않는다.

인스턴스 변수는 객체가 생성될때,  해당 객체에 할당되는 것이다.
`const p1 = new Person('pius');`를 호출하면, 아래의 시나리오를 따르게 된다.

1. {} 빈 객체 생성.
2. 생성자 실행
3. 인스턴스 변수 할당.

```javascript
class Person {
	constructor(name) {
		this.name = name;
	}
	sayHello(){
		console.log(`hello my name is ${this.name}`);
	}
}

const person = new Person('pius');
person.sayHello();
// >>> hello my name is pius
```

## 클래스 원리

```javascript
class Person {
	constructor(name) {
		this.name = name;
	}
	sayHello(){
		console.log(`hello my name is ${this.name}`);
	}
}


console.log(typeof Person);
// >>> Function
console.log(Object.keys(Person.prototype));
// >>> []
// prototype 객체는 조회할 수 없도록 설정이 되어 있음
 
console.log(Object.getOwnPropertyNames(Person.prototype));
// >>> ["constructor", "sayHello"]

const person = new Person('mike');
// person instance
console.log(Object.keys(person));
// >>> ['name']
// contructor에서 만들어지는 변수들은 객체에 직접 할당이된다.

console.log(Person.prototype.constructor === Person);
// >>> true
console.log(Person.prototype.sayHello);
// >>> [Function: sayHello]
```

## getter / setter

```javascript
class Person {
	constructor(name){ 
		this._name = name;
		// 일종의 표시일뿐 실제로 
		// 외부에서는 public 으로 접근가능
	}
	get name() {
		return this._name;
	}
	set name(value){ 
		if(value.length < 4) {
			console.log('name is too short');
			return; 
		}
	}
}
const person = new Person('pius');
console.log(person.name);
// >>> pius
// get 호출

person.name = 'ab';
// >>> name is too short
// set 호출

console.log(person.name);
// >>> pius
```


```javascript
class Person {
	constructor(name){ 
		this._name = name;
	}
// getter만 있을 경우에는 읽기 전용이 되어서
// write 불가능
	get name() {
		return this._name;
	}
}
const person = new Person('pius');
person.name = 'pius712';
console.log(person.name);
// >>> pius

console.log(Object.keys(perosn));
// >>> ['_name']
```

## 상속

클래스의 상속은 프로토타입 기반에서 작성되어있다.


```javascript
class Person {
	constructor(name){ 
		this.name = name;
	}
	sayHello() {
		console.log(`hello ${this.name}`);
	}
}

class Programmer extends Person {
	constructor(name, language){ 
		super(name);
		this.language = language;
	}
}

const p1 = new Programmer('pius', 'js');
p1.sayHello();
```

- default 생성자

생성자는 생략이 가능하다. 생략시 default 생성자 호출됨

```javascript
// 생략시 default 생성자 호출됨
constructor(...args) {
	super(...args);
}
```

### 상속과 프로토타입

```javascript
class Person {
	constructor(name){ 
		this.name = name;
	}
	sayHello() {
		console.log(`hello ${this.name}`);
	}
}

class Programmer extends Person {
	constructor(name, language){ 
		super(name);
		this.language = language;
	}
}

console.log(Object.getPrototypeOf(Programmer.prototype)
						=== Person.prototype);
// >>> true
console.log(Object.getPrototypeOf(Programmer) === Person);
// >>> true

Person.prototype === Programmer.__proto__
// >>> false 
// ????????
```

생성자 함수를 객체를 생성하는 것과 상속을 하는 것에는 차이가 있다.

클래스 문법을 사용해서 객체를 생성하는 경우에 생성된 객체와 클래스 사이의 prototype 관계는 생성자 함수를 통해서 객체를 생성하는 것과 같다.

프로토타입 관계
`클래스 문법 객체 === 생성자 함수 객체`

```javascript
class Person {
	constructor(name){ 
		this.name = name;
	}
	sayHello() {
		console.log(`hello ${this.name}`);
	}
}

class Programmer extends Person {
	constructor(name, language){ 
		super(name);
		this.language = language;
	}
}
let p1 = new Person('pius');
console.log(p1.__proto__ === Person.prototype);
// >>> true
```

**상속의 경우 프로토타입의 관계가 다르다**

```javascript
class Person {
	constructor(name){ 
		this.name = name;
	}
	sayHello() {
		console.log(`hello ${this.name}`);
	}
}

class Programmer extends Person {
	constructor(name, language){ 
		super(name);
		this.language = language;
	}
}
const p1 = Programmer('pius', 'js');

console.log(Object.getPrototypeOf(Programmer.prototype)
						=== Person.prototype);
// >>> true
console.log(Object.getPrototypeOf(Programmer) === Person);
// >>> true

```


```bash
Person          <----      Programmer            p1                 
   |                         |                /
Person.prototype  <----    Programmer.prototype      

```

### 메서드 오버라이딩

```javascript
class Person {
	constructor(name){ 
		this.name = name;
	}
	sayHello() {
		console.log(`hello ${this.name}`);
	}
	getRandom() {
		return Math.floor(Math.random() * 10);
	}
}

class Programmer extends Person {
	constructor(name, language){ 
		super(name);
		this.language = language;
	}
	sayHello() {
		super.sayHello()
		console.log(`my language is ${this.language}`);
		console.log(`Your number is ${this.getRandom()}`);
	}
	getRandom() {
		return 20 + Math.floor(Math.random() * 10);
	}
}

const p1 = new Programmer('pius', 'js');
p1.sayHello();
// >>> hello pius
// >>> my language is js
// >>> Your number is 25
```


## Class field

프로토타입이 아니라 객체에 할당이 된다.

프로토타입 프로퍼티의 객체를 확인해도 그곳에 할당되지 않고, 객체에 할당이 된다.

```javascript
class Person {
	age = 23;
	constructor(name) {
		this.name = name;
	}
	printName = () =>{ 
		console.log(this.name);
	}
	printHello() {
        console.log("sayHello");    
    }	
}

console.log(Person.prototype.age, Person.prototype.printName);
// >>> undefined undefined

console.log(Person.prototype.printHello);
// >>> f printHello() { //...생략  }


const person1 = new Person('pius');
const person2 = new Person('pius712');
person1.age = 100;
console.log(person1.age, person2.age);
```


```javascript
class Person {
	age = 23;
	constructor(name) {
		this.name = name;
	}
	printName1 = function() {
		console.log(this.name);
	}
	printName2 = () =>{ 
		console.log(this.name);
	}	
}
const person1 = new Person('pius');
setTimeout(person1.printName1, 100);
// >>> undefined

setTimeout(person1.printName2, 100);
// >>> pius
```

`setTimeout(person1.printName1, 100);`의 결과가 undefined인 이유는 무엇일까? 분명 함수호출 당시에 객체가 바인딩 되어있는데?

// >>> 함수 호출 당시 (X)
setTimeout의 `첫번째 인자는 호출이 아니라 함수 레퍼런스를 넘겨준것이다.` 함수의 레퍼런스를 넘겨준거고, 이후 setTimeout에 의해 호출되기 때문에, 그 호출 당시에는 바인딩이 없다. 따라서 this가 적용이 안된다.

### super / this

`super` 키워드는 프로토타입을 기반으로 동작한다.

그렇기 때문에, class field로 선언한 메서드의 경우 `super` 키워드로 호출하는 경우 에러가 발생한다.

`this` 키워드를 통해서 호출할 때에도,  class field의 경우 약간 다르게 호출이된다.

1. 객체를 먼저 확인
2. 프로토타입을 확인

위의 순서로 호출되기 때문에, Programmer 클래스를 통해 만든 p1에서 `this.printName()` 호출시에, 상속받은 클래스 필드의 `printName`을 먼저 호출하게 된다.

즉, Person에서의 클래스 필드 `printName`과, Programmer의 메서드 `printName`에서 Person 클래스의 클래스 필드가 우선하게 된다.

이를 해결하기위해서는 Programmer 클래스에서도 클래스 필드를 통해 메서드를 생성해야한다.

```javascript
class Person {
	constructor(name) {
		this.name = name;
	}
	sayHello = () => {
		console.log(`hello my name is ${this.name}`);
	}
	printName = () => { 
		console.log(this.name);
	}	
}

class Programmer extends Person {

	constructor(name, language) {
		super(name);
		this.language = language;
	}
	
	sayHello = () =>  {
		super.sayHello();
		// error 발생지점
		console.log(`I like ${this.language}.`);
	}
	print() {
		this.printName();
	}
	printName() {
		console.log(`printName: ${this.name}`);
	}
}

const p1 = new Programmer('pius', 'js');
p1.sayHello();
// error

p1.print();
// >>> pius

```


## Static

`static` 키워드를 사용하면, 정적 멤버 변수, 정적 메서드를 만들 수 있다.

객체마다 만들어지는 것이 아니라, 클래스에 하나만 만들어지는 것이다.
```javascript
class Person {
	static CURRENT_ID = 1;
	constructor(name, age){
		this.name = name;
		this.age = age;
		this.id = Person.CURRENT_ID++;
	}
	static getYounger(p1, p2){
		return p1.age <= p2.age ? p1 : p2;
	}
}

const p1 = new Person('pius', 29);
const p2 = new Person('bius', 39);
const younger = Person.getYounger(p1, p2);
console.log(younger.name);
// >>> pius
console.log(Person.CURRENT_ID);
// >>> 3
```

정적 멤버 변수, 정적 메서드 또한 상속이 가능하다.
왜냐하면, 상속을 하게 되면 프로토타입 체인으로 연결되어 있기 때문이다.

```javascript
class Person {
	constructor(name, age){
		this.name = name;
		this.age = age;
		this.id = Person.CURRENT_ID++;
	}
	static getYounger(p1, p2){
		return p1.age <= p2.age ? p1 : p2;
	}
}

class Programmer extends Person { }

console.log(Programmer.getYounger);
console.log(Object.getPrototypeOf(Programmer) === Person);


```


## 접근제어
일반적으로 객체 지향언어에서는 public, protected, private을 사용할 수 있는데,
JS에는 public, private이 지원된다.

아직 표준은 아님.

```javascript
class Person {
	#name;
	age;
	constructor(name, age){
		this.#name = name;
		this.age = age;
	}
	#sayHello(){
		console.log(`my name is ${this.#name}`);
	}
}
const p1 = new Person('pius', 29);
p1.#name;
// error
```


```javascript

```

```javascript

```
#javascript/객체
