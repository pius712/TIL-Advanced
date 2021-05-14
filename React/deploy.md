# Deploy

참고 :
- [코드 분할 – React](https://ko.reactjs.org/docs/code-splitting.html#reactlazy)
- https://loadable-components.com/docs/getting-started/
- [Server Side Rendering - Loadable Components](https://loadable-components.com/docs/server-side-rendering/)
- [styled-components: Advanced Usage](https://styled-components.com/docs/advanced#example)
## 배포하기
`npm run build`

리액트의 경우 위 명령어 수행시 build가 되고, 이를 바탕으로 static 파일을 만들어 낸다.



## 환경별 설정

일반적으로 서비스의 경우 dev / staging / live 환경을 따로 만들기 때문에, 이를 고려하여야 한다.

CRA 가이드에 보면 권장하는 방법은 아래와 같다.

`env-cmd` 라이브러리의 경우 env를 환경별 만들어서 스크립트 실행시 사용할 수 있도록 해준다.

```bash
 npm i --save env-cmd
```



우선 로컬환경에서도 사용해야하므로, `npn run start` => `npm run dev`로 변경해주었다.
```json
"script": {
	"dev": "PORT=8080 react-scripts start",
	"build:local" : "env-cmd -f .env.local react-script build",
	"build:dev": "env-cmd -f .env.dev react-scripts build",
	"build:prod": "env-cmd -f .env.prod react-scripts build",
	"start": "node src/server/app.js",
}
```


## 코드 스플릿
@loadable/component 라이브러리를 사용해서 아래와 같이 컴포넌트를 설정하면, 코드 스플릿이 가능.

```typescript
// App.ts
import loadable from '@loadable/component';
const Home = loadable(() => import('pages/home'));
const Auth = loadable(() => import('pages/auth'));
const Profile = loadable(() => import('pages/profile'));
```

빌드시, 하나의 chunk 파일을 여러개로 스플릿하기 때문에 기존보다 chunk 파일이 많아진다.
일반적으로 SPA 서비스가 커지면, js파일이 굉장히 커지기 때문에 로딩 속도가 저하된다. 코드 스플릿을 하면, 이를 나누어서 가져오고, 로드 시점에 가져오기 때문에, 로딩 속도에서 이점을 가진다.

## 서버 사이드 렌더링

### 프로젝트 설정

서버 사이드 렌더링을 하기 위해서는 여러가지 방법이 있다.

* Nextjs 프로젝트로 진행
* webpack, babel config

Cra 버전으로 프로젝트를 진행중이기 때문에 후자의 방법을 선택해야 했는데, webpack, babel 설정을 만지는 방법은 아래와 같다.

* eject
* react-app-rewired (lib)
* customize-cra (lib)
* craco (lib)

eject의 경우 권장되지 않는 방법이라 pass
trends 상에는 2번과 3번이 많지만, cra v4는 아직 지원하지 않는 것으로 보여서 4번으로 채택


### 
```ts
import { renderToString } from 'react-dom/server'
import { ChunkExtractor } from '@loadable/server'

const statsFile = path.resolve('../dist/loadable-stats.json')
const extractor = new ChunkExtractor({ statsFile })
const html = renderToString(extractor.collectChunks(<YourApp />))
const scriptTags = extractor.getScriptTags() // or extractor.getScriptElements();

```


## ReactDomServer

ReactDomServer 객체의 아래 메서드를 사용해서 컴포넌트들 정적 마크업으로 렌더링할 수 있다.

즉, 컴포넌트를 인자로 넣어서 일반 html 파일로 만들어준다는 얘기.

- [ReactDOM.hydrate()](https://ko.reactjs.org/docs/react-dom.html#hydrate)
*  [renderToString()](https://ko.reactjs.org/docs/react-dom-server.html#rendertostring)
*  [renderToStaticMarkup()](https://ko.reactjs.org/docs/react-dom-server.html#rendertostaticmarkup)


### ReactDom.hydrate()

일반적으로 클라이언트 사이드에서는 `ReactDom.render` 메서드를 사용하게 된다.

기능적으로는 `render`와 같은데, 서버사이드 렌더링의 경우에는 HTML 마크업이 이미 만들어진 상태인데, 이렇게 렌더링되어 있는 컨테이너에 이벤트 핸들러들을 연결 해주는 역할을 하게된다.

###  renderToString()


r eact 엘리먼트의 초기 HTML을 렌더링합니다. React는 HTML 문자열을 반환합니다. 빠른 페이지 로드를 위해 초기 요청 시에 서버에서 HTML을 생성하여 마크업을 보내거나, 검색 엔진 최적화를 위해 검색 엔진이 페이지를 크롤링할 수 있도록 하는데 사용할 수 있습니다.

프론트 서버에서 마크업을 만들
이미 서버 렌더링 된 마크업이 있는 노드에서  [ReactDOM.hydrate()](https://ko.reactjs.org/docs/react-dom.html#hydrate) 를 호출할 경우 React는 이를 보존하고 이벤트 핸들러만 연결함으로써 매우 뛰어난 첫 로드 성능을 보여줍니다.

### renderToStaticMarkup()

[renderToString](https://ko.reactjs.org/docs/react-dom-server.html#rendertostring) 과 비슷하지만 data-reactroot와 같이 React에서 내부적으로 사용하는 추가적인 DOM 어트리뷰트를 만들지 않습니다. 여분의 어트리뷰트를 제거함으로써 약간의 바이트를 절약할 수 있으므로 React를 간단한 정적 페이지 생성기로 사용하고 싶은 경우에 유용합니다.
클라이언트에서 React를 사용하여 상호작용이 가능한 마크업을 만들려면 이 메서드를 사용하지 마십시오. 대신 서버에서  [renderToString](https://ko.reactjs.org/docs/react-dom-server.html#rendertostring) 을 사용하고 클라이언트에서  [ReactDOM.hydrate()](https://ko.reactjs.org/docs/react-dom.html#hydrate) 를 사용하십시오.




## 참고

### data-reactroot

data-reactroot 는 attribute의 일종인데, html의 속성은 아니다.

리액트 컴포넌트를 jsx로 작성하여 적는 것은 실제로 호출시에 바벨에 의해서

`React.createElemet()`라는 함수로 호출로 변환된다.

이 함수의 반환 값은 객체이다.

이때, 생성되어있는 객체는 `실제 DOM이 어떻게 되어 있어야 하는가` 에 대한 것을 기술한다고 볼 수 있다.

이때 생성되는 일종의 Virtual Dom에서 사용되는 속성이 `data-XXX` 속성이다.


### 렌더링

============== render phase ================
`React.createElement()` 함수를 통해 호출을 하면,

`React element Tree`가 생성된다.

이렇게 만들어진 트리를 `Diffing Algorithm` 을 통해서  어떤 것이 바뀌었는지를 비교하게된다. (참고: [재조정 (Reconciliation) – React](https://ko.reactjs.org/docs/reconciliation.html))



=============== commit phaset ================
그리고 실제 DOM과 차이가 생긴 부분을 업데이트 (혹은 적용)하게 된다.























#React/deploy