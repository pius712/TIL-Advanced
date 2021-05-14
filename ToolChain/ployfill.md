# Polyfill

## 폴리필이란 무엇인가?
바벨은 최신의 js 코드에서 구형 브라우저에서도 돌아가는 js 코드로 컴파일 해주는 source to source 컴파일러이다.

*하지만, 이는 객체 자체가 지원되지 않는 경우에는 코드를 변환해봐야 소용이 없다.*

회사에서 홈페이지 제작 당시에, 이미지의 지연 로딩 처리를 위해서 `InterserctionObserval` 라는 자바스크립트 객체를 사용하였었다.

당시에 바벨, 폴리필에 대한 지식이 별로 없는 상태여서 바벨 설정이 되어 있으니 인터넷 익스플로러에서 사용해도 문제 없겠지? 하고 익스플로러에서 페이지를 여는 순간, 해당 객체를 찾을 수 없다고 사이트에 아무런 화면이 나타나지 않았다.

바벨과 폴리필에 대해서 이것저것 학습하다보니  폴리필을 담당하는 패키지인  `core-js`는 바벨에 통합되어 있다고 한다.  그렇기 때문에, core-js 만으로도 물론 폴리필 설정을 할 수 있지만, 바벨의 설정으로 core-js를 통한 폴리필 설정이 가능한 것이다.


## 폴리필 설정 방법

폴리필에 대한 설정 방법은 여러 가지가 있는데,
1.  `@babel/polyfill`   (@babel/polyfill은 deprecated 되었다)
2.  `core-js` 패키지를 통해 직접적으로 폴리필을 추가해주는 방식이 있다.  [zloirock/core-js: Standard Library](https://github.com/zloirock/core-js)

실제로 1번의 방식은 아래의 코드와 마찬가지다. 즉, 모든 폴리필 설정들을 추가하는 (Proposal 단계를 제외하고) 방식이다.

```javascript
import 'core-js/stable';
import 'regenerator-runtime/runtime';

```




과거에는 @babel/polyfill 패키지를 직접 전역 스코프에 가져오는(import) 방식으로 바벨 폴리필을 추가했지만  [deprecated 되었습니다.](https://babeljs.io/docs/en/babel-polyfill)  현재는 core-js/stable과 regenerator-runtime/runtime 패키지를 직접 전역 스코프에 삽입합니다. 이러한 바벨 폴리필 삽입 방법은 웹채팅처럼 고객 페이지에 삽입되는 애플리케이션인 경우 고객의 전역 스코프를 오염시키는 문제가 있습니다.

#Toolchain/Polyfill