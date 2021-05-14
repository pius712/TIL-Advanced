# Eslint
일반적인 프로그래밍 언어는 컴파일 타임에 코드의 오류나 린트 작업을 수행한다. 하지만, 동적 언어인 Javascript에서는  이러한 오류들을 잡기 위해서 코드를 실행해야 하는 문제가 있다.

Eslint는 정적 분석 도구의 일종으로,  이러한 한계를 극복하고 정적으로 위의 작업을 가능하게 해준다.

Eslint가 검사하는 항목은 크게 두가지로 나뉜다.

- 코드 품질 (오류, 버그)
- 코드 포맷팅 (들여쓰기 규칙, 최대 너비 등)

## 동작 방식?
ESLint는 Javascript의 코드를 Parser를 통해 분석을 한다.
이 분석 결과는 AST(Abstract Syntax Tree)를 통해 나타나게 된다.
AST는 일반적으로 컴파일러가 소스코드를 분석할때 생성하는 사용하는 것으로, 소스트리를 일정한 기준을 통해 나누어 Tree 자료구조의 형태로 만든 결과물이다.

이렇게 만들어진 AST를 ESLint의 Linter가 유저가 설정한 설정파일을 바탕으로 AST를 순회하면서 검사를 한다.

트리를 순회하는 동안에 설정파일과 맞지 않는 부분은 AutoFix를 해주기도 하고, AutoFix가 불가능한 경우에는 Report를 기본적으로 하게 된다.
커맨드 라인에 자주 뜨는 unused-var 와 같은 메세지가 이러한 Report에 해당한다.


### Parser
default Parser: Espree

디폴트가 아닌 다른 Parser를 사용하기 위해서는 설치를 해주어야 한다. 대표적으로 babal parser가 있다.

Parser에는 Parser Options를 추가적으로 설정할 수 있다. 기본 Espress Parser가 아닌 external parser를 사용하는 경우에는 Parser Options를 설정해주어야 한다.

대표적으로는 ecmaVersion / sourceType / ecmaFeatures 옵션이 존재한다.

- ecmaVersion : ECMAScript 의 버전을 고를 수 있다. 기본적으로는 ES6가 설정되어 있고, 이후의 optional chaining과 같은 문법을 사용하려면 버전을 올려줘야 한다.
- sourceType: `script` , `module` 중에 선택할 수 있고, script가 default이다. react의 경우 모듈 시스템을 사용하기 대문에, 'module'을 사용해야한다.
- ecmaFeatures: 그외에 strict 모드 설정이나, JSX 사용 설정 등을 설정할 수 있다. React의 JSX는 여기서 JSX에 추가적인 semantic이 있기 때문에, React의 JSX를 사용하기 위해서는 `eslint-plugin-react` 라는 플러그인을  추가해주는 것이 좋다.



### Plugins

ESLint 설정 중에는 `extends`라는 것이 있는데, 이를 사용하면 extends를 하는 설정을 가져와서 사용할 수 있다.

Plugin은 써드파티 설정 파일이라고 볼 수 있다.

아래와 같이 설정하면 `./node_modules/` 에 있는 `eslint-plugin-react` 파일을 찾게 된다.

```js
{
    "plugins": [
        "react"
    ]
}
```

이후에 extends를 통해서 해당 플러그인 내부의 설정을 가져올 수 있다.

아래는 `react` 플러그인을 설치 하였고, extends 키워드를 통해 해당 플러그인에서 recommened 되는 룰들을 가져온 것이다. 그리고, 아래에 `rules` 에서는 추가적으로 해당 플러그인 내부의 룰에 대해서 수정할 수 있게 된다.

```js
{
    "plugins": [
        "react"
    ],
	  "extends": [
      'plugin:react/recommended',
    },
	  "rules": {
      'react/display-name': 'off',
  }
}

```

### Extension
ESLint Extension은, 기본적으로 열려있는 프로젝트의 워크스페이스에 설정된 파일을 보고, 해당 파일이 없다면 글로벌로 설정되어 있는 파일을 바라보게 된다.




## ESLint와 프리티어의 차이점?
프리티어와 ESLint의 차이점은 프리티어는 AST 를 자체에는 영향을 주지 않는다. 단지 AST를 바탕으로 코드를 reprint만 해준다.


## Prettier

프리티어는 코드 포맷팅을 해주는 도구이다.

```js 
// 프리티어 적용전
foo(reallyLongArg(), omgSoManyParameters(), IShouldRefactorThis(), isThereSeriouslyAnotherOne());

// 프리티어 적용후
foo(
  reallyLongArg(),
  omgSoManyParameters(),
  IShouldRefactorThis(),
  isThereSeriouslyAnotherOne()
);
```

프리티어는 코드 **스타일** 을 일관성있게 만들어준다.

프리티어의 경우에는 AST(Abstract Syntax Tree) 자체에는 영향을 주지 않는다.


#Toolchain/Eslint
