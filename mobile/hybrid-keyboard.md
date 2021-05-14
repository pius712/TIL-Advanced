# 하이브리드앱과 안드로이드 키보드
## 안드로이드 키보드의 기본 동작

안드로이드 기본 동작은 인풋에 포커스가 있느냐 없느냐에 따라서 키보드가 show / hide 동작을 하게 된다.

안드로이드에서 키보드가 스크린에 올라오는 경우에는, UI 의 공간에 영향을 주게 된다.

이 경우에 어떻게 화면 비율을 맞출 것이냐는 것이 문제가 된다.

아래와 같이 `manifest.xml` 파일에서 이 문제에 대한 설정을 할 수 있게 된다.


```xml
<application ... >
    <activity
        android:windowSoftInputMode="adjustResize" ... >
        ...
    </activity>
    ...
</application>

```


- adjustReisze :
  해당 옵션을 주게 되면, 화면의 사이즈가 줄어들게 된다. 하지만, 이 경우에는 모든 화면에 있는 모든 요소에 접근이 가능하도록 되어, 스크롤을 통해서 화면의 모든 영역을 접근할 수 있게 된다.

- adjustPan: 화면의 영역이 줄어들지 않고, 키보드가 해당 영역을 차지해버린다.  일반적인 경우에는 resize 를 하는 것에 비해 바람직한 방법은 아니다. 왜냐하면, 가려진 윈도우를 접근하기 위해서는 키보드를 접어야 하기 때문이다.


## 안드로이드 키보드와 관련된 문제 2가지 해결 리뷰

키보드 동작과 관련하여 회사 앱의 문제점이 2개가 있었다.

1. 인풋에 포커스 여부를 가지고, 렌더링 여부를 결정하는 버튼

위의 현상들이 생기는 이유는 아무래도, 네이티브 앱이 아니라 하이브리드 앱이라서 네이티브의 소프트 키보드와 정확하게 동작이 일치하지 않기 때문인것으로 보인다.

네이티브가 아니라 웹에서 화면을 렌더링해주어야 하기 때문에, 일부 버튼이나 네비게이션 바들이 `position: fixed` 를 통해서 bottom으로 위치시켜놓은 코드들이 있었다.

문제는 키보드의 input 필드가 활성화 되는 경우에는 이러한 버튼이나 네비게이션 바들이 웹 뷰가 줄어들면서 위로 올라가는 현상이 생겼을 것으로 생각이 된다. (내가 앱을 처음부터 만들지는 않았기 때문에)

이러한 문제를 해결하기 위해 키보드의 input이 활성화 되면, 버튼을 보이지 않도록 conditional 하게 처리한 것이 QA 과정에서 드러나게 되었다.

왜냐하면, 안드로이드의 경우에는 인풋에 포커스가 있어도 아래에 있는 제어바를 통해서 키보드를 내릴 수 있다. 해당 부분을 통해서 키보드를 내리게 되면 인풋에 포커스는 그대로 있기 때문에, 다음으로 넘어가는 버튼이 없어지게 된다.

2. 웹 뷰가 팝업 형식으로 두개가 떠있을때, 뒤에 화면이 플리커링 되는 현상


앱 내에서 웹 뷰를 두개를 띄우는  곳이 있다. 리뷰 작성이나,  로그인과 같은 공간은 메인 웹 뷰 위에 팝업 형태롤 웹 뷰를 하나 더 띄우도록 되어 있다.

문제는

` 키보드의 여닫는 속도 != 웹 뷰의 리사이즈 속도`  때문에 키보드를 닫을 때, 팝업 형태로 띄워진 웹 뷰 뒤편에 있는 메인 웹 뷰가 순간적으로 보여지는 현상이 생기게 된다는 것이다.

## 키보드 여부를 기준으로 렌더링을 변경
버튼이 사라져서 리뷰를 작성하거나 다음으로 넘어가기 위해 버튼을 누르려고 할 때, 버튼이 사라져서 진행을 할 수 없는 상황으로 인해, 고객의 리뷰가 생겼다.

이 문제로 인해서 키보드가 열리고 닫히는 것에 대한 이벤트 리스너를 통해서 처리해보고자 하였다.  아래의 버튼이나 네비게이션바를 키보드 여부를 기준으로 condition을 변경하였다.

`keyboardDidHide` / `keyboardWillHide` 두 종류의 이벤트 리스너를 각각 적용해보며 문제를 해결해보고자 하였으나, 둘 다 문제가 발생.

- keyboardDidHide: 이 경우에는 키보드가 오르내리는 애니메이션이 모두 마친뒤에  버튼이 생기거나 사라졌다.
- keyboardWillShow: 이 경우에는 반대로 오르내리기 전에 버튼이 생기거나 사라졌다.

이 문제는 결과적으로 2번의 문제로 귀속되었다.  Resize가 되어버린 화면 때문에 키보드 아래에서 보이지 않게 렌더링이 적용되어야 할 버튼이나 네비게이션바가 키보드 위에 나타나기 때문에, 이상하게 보인 것이다.

`DidOOO` 보다는 `WillOOO` 메서드를 사용하는 것이 우선적으로는 이후 `adjustPan` 변경 이후에도 더 나은 것이라고 생각하여, 키보드 show / hide 는 `WIllOOO` 메서드로 만든 후 아래의 2번 문제를 해결하였다.


## 왜 `adjustPan` 으로 변경을 했는가?

기존 플리커링 문제와, 1번의 문제는 결국 화면의 resize 때문에 생긴 문제였다.  네이티브 앱이 아니기 때문에 키보드와 관련된 세세한 사항들을 플러그인으로는 해결하지 못한다.

참고)  ionic 의 경우에는 2020년이 되어서야 앱이 resume되고 나서 키보드가 열려 있는지 닫혀있는지 제대로 알 수 있는 기능을 도입했다. 그 이전에는 키보드가 열린상태로 앱을 내리고 올렸을때, 리액트 단에서는 키보드가 열려있다고 판단한다.


위의 2번 문제를 해결하기 위해서 웹 뷰가 리사이즈 되는 것을 막기 위해서 `adjustPan`으로 키보드의 옵션을 변경하였다.

이 문제로 인해서 뒤의 메인 웹 뷰가 보이는 문제는 해결할 수 있었다. 하지만 이 변경으로 인해서 웹 전체에 작은 버그들이 생기게 된다.

회사는 하이브리드 앱 구동을 위해서 cordova 를 사용하고 있는데, 이 코르도바의 키보드 플러그인이 앱을 내렸다가 올렸을 때를 제대로 알지 못한다.

코르도바는 기본적으로 adjustReize를 안드로이드의  default로 생성이 되는 것으로 보이는데, adjustPan으로 바꾸자 app 의 라이프 사이클과 웹 뷰 내의 리액트와의 라이프 사이클이 불일치하게 되어 버렸다.

이로 인해서 앱을 내렸다 올렸다 하는 과정에서 키보드를 기준으로 렌더링하는 화면들이 제대로 동작하지 않게 되었다.

참고) 코르도바의 `cordova-plugin-ionic-keyboard` 플러그인은 키보드가 올라온 상태에서 앱을 내렸다가 올리는 경우에 keyboard가 닫혀있음에도 키보드가 열려있다고 인식을 한다. 이 때문에, 1번의 문제 해결 책이 제대로 동작하지 않는다.

이 동작을 잡아주기 위해서 앱이 내려가는 이벤트에 대한 동작이 필요해졌다.

우선적으로  `document.addEventListener('resume', func)` 를 등록하게 되면, 해당 앱의 라이프 사이클의 'resume' 이벤트를 캐치할 수 있다.

해당 동작에 따라 키보드에 대한 설정을 바꾸어 주었다.


## HOC 도입
관련된 로직이 사용되는 컴포넌트가 많아서, 해당 이벤트 리스너들을 컴포넌트 마다 붙이게 되면, 중복된 코드가 너무 많아지게 된다.

함수형 컴포넌트라면 hooks 를 통해 해결할 수 있겠지만, 레거시 코드들로 인해 여의치 않았다.  이러한 코드들의 중복을 제거하기 위해서 HOC 를 도입하였다.

`withKeyboardDetection` 이라는 HOC를 만들어서 해당 컴포넌트들에 이 함수를 붙여주었다.

```js
const withKeyboardDetection = () => (WrappedComponent) => {
  return class extends React.Component {
    state = {
     // ...
    };
    componentDidMount() {
      window.addEventListener('keyboardWillShow', this.detectKeyboardOpening);
    	// ...
    }
    componentWillUnmount() {
      window.removeEventListener('keyboardWillShow', this.detectKeyboardOpening);
		// ...     
	  }
    detectKeyboardOpened = () => { }
    detectKeyboardOpening = () => { }
    detectKeyboardClosing = () => {}
    appResume = () =>{ }
    render() {
      // ...
		return (
        <WrappedComponent
          {...this.props}
          //.. 생략
			 />
      )
    }
  }
}

// 대상 컴포넌트 
class WrappedComponent extends React.component {
 // ...
};
export default(withKeyboardDetection()(WrappedComponent))
```









#모바일앱

















