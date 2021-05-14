#  Babel
바벨은 자바스크립트 언어를 자바스크립트 언어로 변환해주는 컴파일러이다.  가장 기본적인 기능은 ES6 이상의 문법을 변경해주는 역할을 한다.

Babel is a JavaScript compiler
Babel is a toolchain that is mainly used to convert ECMAScript 2015+ code into a backwards compatible version of JavaScript in current and older browsers or environments. Here are the main things Babel can do for you:
* Transform syntax
* Polyfill features that are missing in your target environment (through  [@babel/polyfill](https://babeljs.io/docs/en/babel-polyfill) )
* Source code transformations (codemods)
* And more! (check out these  [videos](https://babeljs.io/videos.html)  for inspiration)

##  바벨의 동작
1. 코드 파싱
2. 변환
3. 출력

바벨은 우선적으로 코드를 파싱하여 AST(Abstract Syntax Tree) 를 만든다.
-> 이렇게 만들어진  AST를 바벨 플러그인이 수정을 한다.
-> 코드를 출력해낸다.




## Plugin / Preset

### Plugin
바벨은 여러가지 플러그인들을 가지고 있다.
`@babel/plugin-transform-arrow-functions` 이런 플러그인을 설치하면, 화살표 함수를 일반 함수로 변환시켜줄 수 있다.

```js
// sum.js
const sum = (a, b) => a + b;

// dist.js
const add = function (a, b) {
  return a + b;
};
```

```zsh
npx babel sum.js --out-file dist.js --plugins=@babel/plugin-transform-arrow-functions  
```

### Preset
화살표 함수, 템플릿 리터럴, const/let, optional 등의 수 많은 ES6+ 문법들이 있다. 이 모든 문법을 하나하나 플러그인을 통해서 설정할 수는 없다.


프리셋은 말 그대로 미리 세팅되어 있는 플러그인들의 집합으로, 위의 문제를 해결해주는 역할을 한다.

아래는 대표적인 프리셋 목록이다.

* preset-env : ES6+ 문법을 변환해준다.
* preset-react : 리액트의 문법을 변환해주는 역할을 한다.
* preset-typescript : 타입스크립트를 자바스크립트로 변환해준다.







#Toolchain/Babel
