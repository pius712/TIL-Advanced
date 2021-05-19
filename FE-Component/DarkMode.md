
# 리액트(ts) 다크모드 - styled components

요즘 대부분의 플랫폼들이 다크모드를 지원하기 시작하여 다크모드 구현에 대해서 관심을 가지게 되었습니다.  
개인 블로그를 만들면서 다크모드 관련 구현을 하면서 익힌 내용들을 공유하고자 합니다.   

이 글은 시리즈로 구성될 예정이며 아래와 같이 진행될 예정입니다. 

- styled-components 사용
- scss에서 css properties 사용
- emotion 사용 
- SSR 사용

SSR의 경우 그 특성상, FOIT(flash of incorrect theme) 현상이 발생할 수 있어서 별도로 다루겠습니다.   

[코드 구현](https://github.com/pius712/dark-mode) 코드 구현은 좌측 깃허브에 공개되어있습니다.  

# styled-components

우선 styled-components 입니다. 
styled-components를 사용하는 경우에는 context api를 사용하는 것보다 styled-components 자체적으로 지원하는 `ThemeProvider`를 사용하시는게 더 편리합니다. 

typescript를 사용하는 경우, 이에 대한 type을 styled-components에 넘겨줘야하는 등의 문제가 있지만, ThemeProvider를 사용하면 이러한 boilerplate 코드들을 줄일 수 있습니다. 


## 테마를 가져오고 설정하는 hooks 만들기

**요구사항**

1. 테마와, 스위칭 버튼의 핸들러 반환
2. 재방문했을때도, 이전에 설정한 다크모드를 그대로 쓰기 위해서 `localStorage`에 이 값을 저장.
3. 첫 방문 혹은 localStorage에 값이 없는 경우, os의 설정을 따른다. 
4. 스위칭 버튼을 누르면 로컬 스토리지에 값을 갱신해준다. 


### 테마 초기화 작업

우선 Lazy initial state 를 통해서 state 를 초기화 시켜주는데, 이 초기화 콜백은 위 요구 사항 2, 3번을 구현한다. 

```typescript
const useTheme = (): [typeof theme, typeof toggleTheme] => {

    const getInitialTheme = useCallback(() => {
        // localStorage에서 'app_theme' 키의 값을 가져온다. 
        let theme = window.localStorage.getItem('app_theme') as 'light' | 'dark' | null;
        const INVALID_THEME = theme !== 'light' && theme !== 'dark';
        
        // 테마가 null이거나 light, dark가 아닐 경우에 운영체제의 테마로 설정해준다.
        if (!theme || INVALID_THEME) {
            const {matches} = window.matchMedia('(prefers-color-scheme: dark)');
            theme = matches ? 'dark' : 'light';
        }

        return theme;
    }, []);
    
    const [theme, setTheme] = useState(getInitialTheme);
    // ...
}
```

### 버튼 핸들러 구현

버튼을 누르면 테마를 변경해주는 이벤트 핸들러를 구현합니다. 

아래는 theme의 값 자체가 필요한 것이 아니라, theme의 이전 값만을 알면 되기 때문에, deps를 빈 배열로 주었습니다. 
만약, theme 값을 직접 받아 사용한다면, deps를 채워주어야 하는데, 이는 theme이 바뀔때마다 toggleTheme 핸들러를 갱신해야합니다. 

> 참고) 이 경우에는 크게 성능차이가 나지 않습니다. 하지만, 실제 theme의 값을 통해 계산하는 것이 아니라 이전의 값을 통해 계산만 하면 되기 때문에 콜백을 사용하였습니다. 

```typescript
import {useCallback, useEffect, useState} from "react";
const useTheme = (): [typeof theme, typeof toggleTheme] => {
    
    // ... 생략
    const toggleTheme = useCallback(() => {
        setTheme((prevTheme) => prevTheme === 'dark' ? 'light' : 'dark');
    }, [])
}

```

### theme 변화에 따른 로컬스토리지 갱신
```typescript

const useTheme = (): [typeof theme, typeof toggleTheme] => {

    // ... 생략
    useEffect(()=>{
        window.localStorage.setItem('app_theme', theme);

    }, [theme])

    return [theme, toggleTheme]
}

export default useTheme;
````

## 테마 만들기 

테마는 다크모드와 라이트모드에 따라 설정해줍니다. 

일반적으로 사내에 있는 `typography`를 따르게 될텐데, 이를 라이트모드와 다크모드에 따라 분리해줍니다. 
간단하게 다크모드에 집중하기 위해서 배경(bg)와 글자(font)만을 설정하도록 하겠습니다. 

CSS Properties를 사용하는 방법은 아래에서 소개해드리겠습니다. 
지금은 ts 객체로 테마를 만들겠습니다. 

```typescript
interface Theme {
    bgColor: string;
    fontColor: string;
}
interface ThemeGroup {
    light:Theme;
    dark: Theme;
}
/**
 * @light theme
 */

export const light:Theme = {
    bgColor : '#fff',
    fontColor : '#000',
}

/**
 * @dark theme
 */

export const dark:Theme = {
    bgColor : '#000',
    fontColor : '#eee',
}

const theme:ThemeGroup = {
    light,
    dark,
}

export default theme;
```


## App.tsx 파일 설정

switch의 경우 react-switch 라이브러리를 사용하였습니다.  
switch의 경우 라이브러리를 직접 import 하지 않고, `components/common/`에서 래핑해주었습니다.  
라이브러리를 직접 import하지 않고 래핑하고, interface를 맞춰줌으로써 의존성을 낮출 수 있습니다.  
이후 라이브러리를 교체할 때도, 하나의 디렉터리에서만 작업하면 되기 때문에 유지보수성도 증가합니다.   
> 참고) https://alexkondov.com/tao-of-react/#wrap-external-components

아래와 같이 `ThemeProvider` 를 사용해서 테마의 객체를 넘겨주면, 아래의 styled-components에서는 이 값을 사용할 수 있습니다.  


```typescript
// app.tsx
import React from 'react';
import GlobalStyle from 'styles/GlobalStyle';
import Profile from "components/common/profile";
import ThemeSwitch from "components/common/theme-switch";
import styled, {ThemeProvider} from 'styled-components';
import {default as THEME} from "styles/Theme";

import useTheme from "hooks/useTheme";

const App = () => {
    const [theme, onToggle] = useTheme();
    
    return (
        <React.Framgment>
            <ThemeProvider theme={THEME[theme]}>
                <GlobalStyle/>
                <MainContainer>
                    <ThemeSwitch checked={theme === 'dark'} toggleSwitch={onToggle}/>
                    <ProfileContainer>
                        <Profile/>
                    </ProfileContainer>
                </MainContainer>
            </ThemeProvider>
        </React.Framgment>
    );
}
```

## GlobalStyle과 Profile 구현

아래와 같이 GlobalStyle의 body에 props를 통해 theme 객체를 받은 후, background 속성을 동적으로 추가해주었습니다. 

```typescript
// styles/GlobalStyle.tsx
import {createGlobalStyle} from "styled-components";
import {Theme} from "styles/Theme";

const GlobalStyle = createGlobalStyle<{
    theme:Theme,
}>`
    body {
      background-color: ${( props) => props.theme.bgColor};
    }
`
export default GlobalStyle;
```

프로필의 경에서 사용하는 styled-components 일부입니다. 아래와 같은 방식으로 ThemeProvider로부터 받은 값을 설정해주는 모습입니다. 

```typescript
// components/common/profile

// ...생략
const Desc = styled.p`
  color: ${(props) => props.theme.fontColor};
`
```



 다크모드 그 자체만을 구현하기 위한 것으로 군더더기는 모두 제거하여 구현하였습니다. 궁금한점 있으면 댓글남겨주세요.


## css variable


## emotion

