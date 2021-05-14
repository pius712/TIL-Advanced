# Post ES6
- nullish coalescing
- short hand property
- computed property
- spread operator
- destructuring

## nullish coalescing
### 논리 연산
```javascript
string - '' === false
number - 0 & NaN === false
```

`&&`

```javascript
const c1 = 123;
const c2 = 'abc';
const v1 = c1 && c2;
const v2 = c1 && c2 && 0;
const v3 = c1 && 0 && c2;
console.log({ v1, v2, v3 });

const v4 = c1 || c2;
const v5 = '' || c2;
console.log({v4, v5});

// 논리 연산자의 값은 boolean이 아닐 수 있다. 
// 왜냐하면 v4, v5의 경우 아래와 같은 값을 가지기 때문이다. 
// { v4: 123, v5: 'abc'} 

// 이경우 !!를 붙여주면 boolean 값이 된다. 
const v6 = !!(c1 && 0 && c2);
const v7 = !!(c1 || c2);
console.log({ v6, v7});
>>> { v6: false, v7: true }
```

`||`

```javascript
const price = 0; 
const name = '';

// 뒤에 부분이 기본값 처럼 사용될 수 있다. 
// 아래 nullish coalescing 참고
const price2 = price || 1000; 
const name2 = name || '이름을 입력해주세요';
```

###  nullish coalescing

```javascript
const person = {};

// nullish coalescing
const name = person.name ?? 'unknown';

// 동작 방식
const name = 
	person.name === undefined || person.name === null 
	? 'unknown' : person.name;

```

### nullish coalescing vs `||`

빈문자열이나 0의 경우에는 둘이 다르게 동작한다.
```javascript
const product = { name: '', price: 0};

const name1 = product.name ?? '상품명을 입력하세요';
const price1 = product.price ?? 1000;
console.log({ name1, price1 });
>>> {name1: "", price1: 0}

const name2 = product.name || '상품명을 입력하세요';
const price2 = product.price || 1000;
console.log({ name2, price2 });
>>> {name2: "상품명을 입력하세요", price2: 1000}

```


## Short hand property

```javascript
const name = 'pius';

// before
const obj = {
	age: 29,
	name : name, 
	getName: function getName() {
		return this.name;
	}
};

// after
const obj = {
	age: 29,
	name,
	getName() {
		return this.name;
	}
```


## Computed Property
```javascript
// before ??
function makeObject1(key, value) {
	const obj = {};
	obj[key] = value;
	return obj;
}
// after
function makeObj2(key, value) {
	return { [key] : value };
}
```

```javascript

```

## Spread operator
```javascript
// before. 각각 입력
Math.max(1, 3, 7, 9);

// after 
const numbers == [1, 3, 7, 9];
Math.max(...numbers);
```

```javascript
const arr1 = [1, 2, 3];
const obj1 = { age: 23, name: 'pius'};

// 아래 처럼 복사 가능
const arr2 = [...arr1];
const obj2 = {...obj1};
arr2.push(4); // arr1 영향 X
obj2.age = 29;  // obj1 영향 X
```

```javascript
const obj1 = { age: 21, name: 'pius' };
const obj2 = { hobby: 'programming' };

const obj3 = { ...obj1, ...obj2 };
console.log(obj3);
>>> { age: 21, name: 'pius', hobby: 'programming' };
```

```javascript
const obj1 = {x:1, x:2, y: 'a' }};
const obj2 = {...obj1, y:'b'};
console.log({ obj1, obj2 });

{ obj1: { x2, y: 'a'}, obj2: { x: 2, y: 'b' }};
```

## Destructuring

### 배열 비구조화 할당
```javascript
const arr = [1, 2];

const [a, b] = arr;
console.log(a);
console.log(b);
```

```javascript
const arr = [1, 2];

let a, b;
[a, b] = arr;
```

```javascript
const arr = [1];
// 기본값 설정. undefined -> 기본값 사용
const [a = 10, b = 20] = arr;
console.log({a, b});
>>> {a: 1, b = 20}
```


```javascript
let a = 1;
let b= 2;

// swap 기능 사용 가능.
[a, b] = [b, a];
console.log({a, b});
>>> { a: 2, b: 1 } 
```


```javascript
console arr = [1,2,3];
const [a, ,c] = arr;
console.log({a, c});
>>> {a: 1, c: 3 };
```

```javascript
const arr = [1,2,3];
const [first, ...rest1] = arr;
console.log(rest1);
>>> [2, 3]

const [a,b,c ...rest2] = arr;
console.log(rest2);
>>> []
```


### 객체 비구조화 할당

```javascript
const obj = { age: 29, name: 'pius' };
const { age, name } = obj;
console.log({ age, name});
>>> { age: 29, name: 'pius' }
```


객체 비구조화 할당은 순서가 중요하지 않다.

```javascript
const obj = { age: 29, name: 'pius' };
const { age, name } = obj;
const { name, age } = obj;
const { a, b } = obj; // a,b 모두 undefined  
```

원래 속성과 다른 이름으로 변수 생성

```javascript
const obj = { age : 29, name: 'pius'};
const {age: myAge, name} = obj;
console.log(myAge);
console.log(age); // reference Error
```

undefined인 경우에만 기본값 설정된다.
```javascript
const obj = { age: undefined, name: null, job: 'programmer'};
const { age = 0, name = 'noName', job = 'lawyer' } = obj;
console.log({age, name, job});
>>> {age: 0, name: null, job: "programmer"}
```


```javascript
function getDefaultAge() {
	console.log('hello');
	return 0;
}
const obj = { age: undefined, grade: 'A' };
const { age = getDefaultAge(), grade } = obj;
console.log(age);
>>> hello
>>> 0 
```


```javascript
const obj = { age: 29, name: 'pius', job: 'programmer'};
const { age, ...rest } = obj;
console.log(rest);
>>> { name: pius, job: 'programmer' };
```

```javascript
const people = [ 
	{ age: 29, name: 'pius' },
	{ age: 20, name: 'bius' },
];
for (const {age, name} of people ){
	// ...
}
```

### 고급

```javascript
const obj = { name: 'pius', univ: { name: 'cau' }};
const {
	name, 
	univ: { name: univName},
} = obj;
console.log({name, univName}); 
>>> {name: 'pius', univName: 'cau'}
console.log(univ); // reference Error
```


```javascript
const [ {props: x} = { props: 123} ] = [];
console.log(x);
>>> 123

// 아이템이 하나 있기 때문에, 기본 값이 할당되지 않는다. 
// 하지만 빈 객체에는 props 속성값이 없기 때문에, 
// x는 undefined
const [ {props: x} = { props: 123} ] = [{}];
console.log(x);
```

with computed property
반드시 별칭 사용.
```javascript
const idx = 1;
const {[`key${idx}`]: valueOfTheIdx} = {key1: 123};
console.log(valueOfTheIdx);
>>> 1
```


```javascript
const obj = {};
const arr = [];
({foo : obj.prop, bar: arr[0]} = {foo : 123, bar: true});
console.log(obj);
>>> { prop: 123 }
console.log(arr);
>>> [123]
```


## Optional Chaining

```javascript
const person = null;
// before
const nmae = person && person.name;
// after
const name2 = person?.name;

// 동작 방식
const name = person === null || person === undefined ? undefined : person.name;
```

### 함수에 사용

```javascript
const person = {
	getName: () => 'abc',
}
const name = person.getName?.();
console.log(name); 
```

```javascript
function loadData(onComplete) {
	console.log('loading...');
	onComplete?.();
}
loadData();
```

### 배열에 사용

```javascript
const person = {friends: null, mother: null };

const firstFriend = person.friends?.[0];
// firstFriend - undefined
const prop = 'name';
const name = person.mother?.[prop]

// =============
const person = {friends: [1,2,3], mother: {name: 'pius'} };

const firstFriend = person.friends?.[0];
// firstFriend - 1
const prop = 'name';
const name = person.mother?.[prop]
// name - pius
```

```javascript
const name = 
	person && 
	person.friends && 
	person.friends[0] && 
	person.friends[0].mother &&
	person.friends[0].mother.name; 
	
const name2 = person?.friends?.[0]?.mother?.name;

```

## Generator
```javascript

```

```javascript

```


## Nullish Coalescing
```javascript

```



#javascript/Post_ES6