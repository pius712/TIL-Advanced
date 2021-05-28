# enum

enum 객체를 사용하면 이름을 부여하여된 상수들을 하나의 집합으로 다룰 수 있게 된다.

## 목차

- Numeric Enum
- String Enum
- 양방향 맵핑
- const enum


## Numeric

`enum` 키워드를 통해서 enum 객체를 정의할 수 있다. 

아래 코드의 경우 바나나는 1 값을 가지고 그 아래에 서술되는 값들은 1씩 auto-increment 된다.

즉, `Fruit.Apple === 2`, `Fruit.Melon === 2` 가 된다. 
```typescript
enum Fruit {
    Banana = 1,
    Apple,
    Melon,
}

```

만약에 초기화하는 코드가 없으면, 첫번째 값이 0으로 시작해서 auto-increment가 된다. 

```typescript
enum Direction {
    Up,
    Down,
    Left,
    Right,
}
```

enum을 사용하는 방법은 그냥 enum 객체의 프로퍼티로 접근해서 사용하면된다. 

```typescript
enum UserResponse {
    No = 0,
    Yes = 1,
}
function respond(recipient: string, message: UserResponse): void {
// ...
}
respond("Princess Caroline", UserResponse.Yes);
```

Numeric enums can be mixed in computed and constant members (see below). 
The short story is, enums without initializers 
either need to be first, or have to come after numeric enums initialized with numeric constants or other constant enum members.
In other words, the following isn’t allowed:

```typescript
enum E {
    A = getSomeValue(),
    B,
//    Enum member must have initializer.
}
```


## String Enum


String Enum의 경우에는 모든 멤버를 초기화 해주어야 한다. 

string enum은 serialize가 쉽기 때문에, 이후에 에러가 나거나 디버깅을 할 때, 
이 값(우측) 자체가 문자로 나오기 때문에, 이해하기가 쉽다. 
(numeric의 경우에는 숫자로 나오기 때문에 의미있는 값으로 해석하기가 쉽지 않다.)

```typescript
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```



## 양방향 맵핑

numberic enum을 사용하는 경우에는 `value - key` 관계가 양방향으로 성립한다. 

이러한 이유로, enum 객체의 키를 가지고 오고 싶을때 `Object.keys` 함수를 사용하면 아래와 같이 모든 값이 다 나오게 된다.

numeric enum을 사용하는 경우에는 `key-value` 를 양방향으로 맵핑을 하게 되어 이런 현상이 발생한다.

즉, `Fruit[Banana]`, `Fruit[1]` 모두 접근이 가능한 형태가 되기 때문이다.

```typescript
enum Fruit {
    Banana = 1,
    Apple,
    Melon,
}
console.log(Object.keys(Fruit));
// ["1", "2", "3", "Banana", "Apple", "Melon"] 
```

아래는 컴파일된 js 파일이다.

```javascript
"use strict"
var Fruit;
(function (Fruit) {
    Fruit[Fruit["Banana"] = 1] = "Banana";
    Fruit[Fruit["Apple"] = 2] = "Apple";
    Fruit[Fruit["Melon"] = 3] = "Melon";
})(Fruit || (Fruit = {}));
console.log(Object.keys(Fruit));
```

만약에 numeric enum을 사용할때, 아래와 같은 상황이 생길 수 있다. 
- 좌측의 key 값 만이 필요한 경우 
  
```typescript
 function getKeyOfEnum(enumObj : any) {
    return Object.keys(enumObj)
        .filter(key => isNaN(Number(key)))
} 
```
- 혹은 해당 값이 value 인지 검증이 필요한 경우.

```typescript
 function isValidKeyOfEnum(enumObj : any ,  enumKey: number | string) {
    return Object.keys(enumObj)
        .filter(key => isNaN(Number(key)))
        .some(key => key === enumKey)
}

enum Fruit {
    Banana = 1,
    Apple,
    Melon,
}

console.log(isValidKeyOfEnum(Fruit, 'Banana')); // true
console.log(isValidKeyOfEnum(Fruit, 'Apple'));  // true
console.log(isValidKeyOfEnum(Fruit, 1)); // false
console.log(isValidKeyOfEnum(Fruit, 2)); // false
```

- 혹은 key가 맞는지 검증이 필요.

```typescript
function isValidValueOfEnum(enumObj: any, value: number | string) {
    return Object.keys(enumObj)
        .filter(key => isNaN(Number(key)))
        .some(key =>enumObj[key] === value);
}

enum Some {
    key = 1,
}

// Some['key'] = 1;
// Some[1] = 'key'; 

isValidValueOfEnum(Some, 'key1'); // false 
isValidValueOfEnum(Some, 1); //  true
```



## const enum 


`const enum` 사용시  컴파일 후에 enum 객체가 남지 않아서 번들 파일을 줄일 수 있다. 

이 경우 위의 `getIsValidEnumValue` 함수를 사용할 수 가 없는데, 그 이유는 enum 객체가 사라지기 때문이다. 



```typescript
const enum Direction {
    Up,
    Down,
    Left,
    Right,
}

let directions = [
    Direction.Up,
    Direction.Down,
    Direction.Left,
    Direction.Right,
];
```

아래와 같이 enum 객체가 사라진다. 

```javascript
"use strict";
let directions = [
    0 /* Up */,
    1 /* Down */,
    2 /* Left */,
    3 /* Right */,
];
```