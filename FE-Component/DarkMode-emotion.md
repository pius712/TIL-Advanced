
# DarkMode - emotion 

이번에는 emotion을 활용하여 다크모드를 만들어 보겠습니다.

emotion을 사용하기 위해서는 몇몇 설정 방법이 필요합니다. 

자세한 코드는 [깃허브](https://github.com/pius712/dark-mode)에서 확인할 수 있습니다.  

**환경**

- typescript
- create-react-app

## 목차 

- 환경설정 
  - 설치
  - css prop 설정 
  - typescript 설정
- hooks 만들기 및 테마 만들기
- App.tsx 파일 설정
- GlobalStyle과 Profile 구현
  - type error 발생 및 해결
- 추가설명
  - babel / pragma 설정의 의미 ? 
  - d.ts 파일 ?




## 환경설정

> 해당 프로젝트에서는  css prop을 사용할 예정입니다. 

### 설치

emotion react 설치
`npm install --save @emotion/react`



### css prop 설정  

css prop을 사용하기 위해서는 2가지 방식중에 하나를 선택해야합니다. 

1.  babel plugin 사용
2.  JSX Pragma


저는 create-react-app으로 프로젝트를 생성하였지만, 2번 방식을 사용하게 되면 css prop을 사용하는 파일마다,
아래와 같은 코드를 작성해주어야 합니다. 

```javascript
/** @jsx jsx */
import { jsx } from '@emotion/react'
```

따라서 저는 `craco` 라는 라이브러리를 사용하여, cra 프로젝트를 eject하지 않고 설정파일을 오버라이드 하는 
방법을 선택했습니다. 


1. craco 라이브러리 설치

  `npm install @craco/craco --save`

2. emotion babel plugin 설치

css prop 사용을 위한 preset:  
`npm i --save-dev @emotion/babel-preset-css-prop`  

optimization 을 위한 plugin:  
`npm install --save-dev @emotion/babel-plugin`

3. craco 설정하기  
```javascript
// [project-root]/craco.config.js
module.exports = {
  babel : {
    "presets": ["@emotion/babel-preset-css-prop"],
    "plugins": ["@emotion"]
  }
}
```

4. 스크립트 변경하기

package.json 에서  `react-scripts` -> `craco` 로 변경하기 

```json
"scripts" : {
    "start": "craco start",
    "build": "craco build",
    "test": "craco test",
  "eject": "craco eject"
  }
```


### typescript 설정

추가적으로  typescript를 사용하면 tsconfig.json 을 수정해주어야 합니다. 

```json
{
  "compileOptions" : {
    // ...  생략
    "jsxImportSource": "@emotion/react",
  }
}
```





##  hooks 만들기 및 테마 만들기

hooks 구현과 테마 구현의 경우 아래의 링크와 동일합니다. 상세한 내용은 아래 링크에서 확인부탁드립니다. 

[다크모드 - styled-components 편](https://velog.io/@pius712/리액트-다크모드-styled-components)

```typescript
// useTheme.ts
import { useCallback, useEffect, useState } from 'react'

const useTheme = (): [typeof theme, typeof toggleTheme] => {
  const getInitialTheme = useCallback(() => {
    let theme = window.localStorage.getItem('app_theme') as
      | 'light'
      | 'dark'
      | null
    const INVALID_THEME = theme !== 'light' && theme !== 'dark'

    if (!theme || INVALID_THEME) {
      const { matches } = window.matchMedia('(prefers-color-scheme: dark)')
      theme = matches ? 'dark' : 'light'
    }

    return theme
  }, [])

  const [theme, setTheme] = useState(getInitialTheme)

  const toggleTheme = useCallback(() => {
    setTheme((prevTheme) => (prevTheme === 'dark' ? 'light' : 'dark'))
  }, [])

  useEffect(() => {
    window.localStorage.setItem('app_theme', theme)
  }, [theme])

  return [theme, toggleTheme]
}

export default useTheme
```

```typescript
// styles/Theme.ts
export interface Theme {
  bgColor: string;
  fontColor: string;
}
interface ThemeGroup {
  light: Theme;
  dark: Theme;
}
/**
 * @light theme
 */

export const light: Theme = {
  bgColor: '#fff',
  fontColor: '#000',
};

/**
 * @dark theme
 */

export const dark: Theme = {
  bgColor: '#000',
  fontColor: '#eee',
};

const mode: ThemeGroup = {
  light,
  dark,
};

export default mode;
```


## App.tsx 파일 설정

 styled-components 편과 비슷합니다. emotion의 경우에도 ThemeProvider 컴포넌트를 제공해주고 있기 때문에, 이를 이용해서 
만들어둔 `THEME` 객체를 넘겨줍니다. 

```typescript
import React from 'react';
import { css, Global, ThemeProvider } from '@emotion/react';

import Profile from 'components/profile';
import ThemeSwitch from 'components/theme-switch';
import useTheme from 'hooks/useTheme';
import GlobalStyle from 'styles/GlobalStyle';
import {default as THEME} from 'styles/Theme';

const App = () => {
  const [theme, onToggle] = useTheme();

  return (
    <>
      <ThemeProvider theme={THEME[theme]}>
        <Global styles={GlobalStyle(THEME[theme])} />
        <div css={MainContainer}>
          <ThemeSwitch checked={theme === 'dark'} toggleSwitch={onToggle} />
          <div css={ProfileContainer}>
            <Profile />
          </div>
        </div>
      </ThemeProvider>
    </>
  );
};

const MainContainer = css`
  margin: 0 auto;
  max-width: 780px;
`;
const ProfileContainer = css`
  margin: 1rem;
`;
```



## GlobalStyle과 Profile 구현

 emotion의 css props를 사용하는 경우, ThemeProvider로 넘겨준 value를 받는 방법이 다릅니다. 

- styled-components의 경우 styled로 감싸진 컴포넌트에서 바로 props를 넘겨 받을 수 있다. 
- emotion의 경우 
  - css props에 화살표 함수를 사용하여, theme 인자를 넘겨줍니다. 
  - nesting되어 직접 전달할 수 없으면 `useTheme`이라는 내장 함수를 사용합니다. (Context Api와 비슷합니다)
  

```typescript
import {css} from "@emotion/react";
import {Theme} from "styles/Theme";


const GlobalStyle = (theme :Theme) => (css`
  body {
    background-color: ${theme.bgColor}
  }
`)

export default GlobalStyle;
```

```typescript
import * as React from 'react';

import { css, useTheme } from '@emotion/react';
import { Theme } from 'styles/Theme';

const Profile: React.FC = () => {
  const theme = useTheme();

  return (
    <div css={ProfileBlock}>
      <div css={IntroduceContainer(theme)}>
            {/* 생략 */}
      </div>
    </div>
  );
};

export default Profile;

const ProfileBlock = css`
  display: flex;
`;

const IntroduceContainer = (theme: Theme) => css`
  display: flex;
  flex-direction: column;
  color: ${theme.fontColor};
`;
```

### type error 발생 및 해결

ThemeProvider에서 전달해준 테마를 emotion에서 제공하는 `useTheme` 을 통해서 받은 테마의 값과 
제가 정의한 테마의 타입이 맞지 않아서 에러가 발생하였습니다. 

따라서 이를 css prop의 화살표 함수의 인자로 전달하는 경우 에러가 납니다. 

type을 찾아보니, emotion의 `useTheme`의 반환값인 `Theme` 인터페이스가 비어(empty) 있었습니다. 

[공식문서](https://emotion.sh/docs/typescript)에 따르면, 이는 type-safety를 위해 의도된 것으로, 
이를 재정의하라고 되어 있었습니다. 

그리하여, 기존에 제가 정의한 Theme과 모양을 맞추어 사용하였습니다. 

```typescript
declare module '@emotion/react' {
  export interface Theme {
    bgColor: string;
    fontColor: string;
  }
}
```


## 추가 설명

### babel / pragma 설정의 의미? 

### d.ts 파일 ? 

