# DarkMode - emotion

이번에는 css properties를 활용하여 다크모드를 만들어 보겠습니다.

css properties는 css variable로 별도의 설정이 필요하지는 않습니다. 
다만 `node-sass`를 설치하여야 합니다. 

자세한 코드는 [깃허브](https://github.com/pius712/dark-mode)에서 확인할 수 있습니다.

**환경**

- typescript
- create-react-app

## 목차

- 테마 만들기
- util 함수 만들기
- hooks 만들기 
- App.tsx 파일 설정
- GlobalStyle과 Profile 구현
    - type error 발생 및 해결


##  테마 만들기

이번 챕터는 이전의 `styled-components`와 `emotion` 편과는 다르게 테마부터 먼저 보려고 합니다.

css properties는 변수처럼 사용할 수 있습니다. `--variable` 형태로 선언하고, `var(--variable)` 과 같은 방식으로 사용할 수 있습니다. 

각각의 모드(light, dark)를 클래스로  `.light` , `.dark` 와 같이 만들어주고, 이 안에 변수를 선언해줍니다. 

그리고 body태그에  `background-color : var(--bg-color)`로 선언하게 되면, 아래처럼 설정이 됩니다. 

- light 클래스가 붙었을때, `#ffffff`
- dark 클래스가 붙었을때, `#363c48`

 이렇게 설정을 하게 되면, 트리의 하위에서는 `scss` `module.scss` 에 상관없이 변수에 접근할 수 있게 됩니다.   
따라서, 이 곳에서 typography를 include, 혹은 작성하는 방법으로 모드에 따른 타이포그래피를 설정할 수 있습니다. 


 아래와 같이 body 태그에 클래스를 변경해주는 방식을 택하게 되면, dom 객체에 직접 접근해야 하기 때문에, 
여러가지 문제(깜빡임 현상)가 발생할 수 있는데, 아래에서 차차 해결하도록 하겠습니다. 

```scss
// global.scss
body {
  background-color: var(--bg-color);
}

body.light {
  --bg-color : #ffffff;
  --font-color : #363c48;
}

body.dark {
  --bg-color : #363c48;
  --font-color : #ffffff;
}
```

## util 함수 만들기 

 추후에, dom에 직접 접근해야하기 때문에, dom 과 관련된 util 함수를 정의하겠습니다. 

```typescript
// utils/dom.ts

const BODY = 'body';

export const getElement = (selector: string) =>
        document.querySelector(selector);

export const addClass = (element: Element, className: string) =>
        element.classList.add(className);

export const removeClass = (element: Element, className: string) =>
        element.classList.remove(className);
export const hasClass = (element: Element, className: string) =>
        element.classList.contains(className);

export const getBody = () => getElement(BODY);

export const addClassToBody = (className: string) => {
  const body = getBody();
  if (body) {
    addClass(body, className);
  }
};

export const removeClassToBody = (className: string) => {
  const body = getBody();
  if (body) {
    removeClass(body, className);
  }
};

export const hasClassOfBody = (className: string) => {
  const body = getBody();
  if (body) {
    return hasClass(body, className);
  } else {
    return false;
  }
};

```

##  hooks 만들기



##  App.tsx 파일 설정

##  GlobalStyle과 Profile 구현
    - type error 발생 및 해결