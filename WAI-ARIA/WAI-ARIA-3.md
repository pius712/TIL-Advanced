#  웹 접근성 고찰 3 - 입력

## Implicit Submission

 <form> 태그 내부에서 "enter" 키를 누르면 암묵적으로 해당 form 태그에 있는 첫번째 submit button을 찾아서 submit을 하게 되는데 이를 Implicit Submission이라고 부른다. 

```jsx
<form onSubmit={handleSubmit}>
	<input value={input} onChange={onChangeInput}> 
	<button type="submit">제출하기</button>
	 //<input type="submit" /> 
</form>
```

하지만 submit button이 없는데, input 필드가 2개 이상인 경우에는 Implicit Submission이 동작하지 않는다.

```jsx
// 해당 경우에는 input에서 enter를 눌러도 submission이 일어나지 않는다.  
<form onSubmit={handleSubmit}>
	<input /> 
	<input /> 
</form>
```


> 참고: <input type="submit" /> 과 <button type="submit">의 차이
> <input type="submit" /> 과 <button type="submit"> 의 차이는 컨텐츠 소유 가능 유무이다. <button> 태그를 사용하는 경우에는 버튼 내부에, 아이콘이나 텍스트 마크업이 가능한 반면, <input type"submit"/> 의 경우에는 이러한 것이 불가능하다. IE 의 아주 낮은 버전 (예를들어, 6, 7) 에서는 <button> 태그가 문제가 될 수는 있으나, 최신 브라우저에서는 문제가 없다.


## No Submission button?

일부 개발자들은 위와 같은 동작을 이해하지 못하는 경우가 있다. 그리하여, 불필요한 코드를 작성하는 경우도 많다.

```jsx
// 아래는 위의 Implicit Submission을 이해하지 못하고, 
// input에 onKeyPress 이벤트에 코딩을 한 경우로, 
// 불필요한 코드가 사용되었다. 
<form>
	<input onKeyPress={(event) => {
  		if (event.key === 'Enter') {
    		return handleClick();
 		 } else {
    		return false;
  		}
	}}/>
	<button onClick={handleClick}>
</form>

```

혹은, 일부 웹 사이트에서는  submission button을 생략하는 경우가 있다.

이 경우


## No Form tag?
자바스크립트로 개발을 하다보면, <input> 태그와 <button> 태그를 이벤트 핸들러에 잘 바인딩만 해두면 기능적으로는 폼 데이터 전송에 아무런 문제가 없어 보인다.

실제로 <form> 태그가 없이도 input을 사용하는 것은 스펙상에도 문제가 없다. 다만 해당 input의 주인 (input을 감싸는 form)이 없다는 것뿐이다.

하지만 `<form>` 태그를 적어주는 것은 웹 접근성 관점에서 필요하다. <form> 내부에서 input을 입력하던 중, 스크린 리더 사용자가   해당 form에 대한 instruction을 네비게이션 하고자 할때, 해당 form 태그가 없는 경우에 이러한 기능을 수행하는데 문제를 겪을 수 있다.

따라서, form 태그를 명시해주고, 해당 form에 대한 instruction을 제공하는 것이 좋다.

[G184: Providing text instructions at the beginning of a form or set of fields that describes the necessary input | Techniques for WCAG 2.0](https://www.w3.org/TR/2014/NOTE-WCAG20-TECHS-20140916/G184)




## Label

### TLDL

<label>을 사용할 때는, 암시적 바인딩이나, for attribute를 사용하는 것이 가장 권장되는 방식이다.


### Label 사용법

모든 input은 그것과 매칭되는 label이 필요하다. 스크린 리더를 읽는 사람은 `<label>` 태그가 없는 경우에는 유저가 포커싱하고 있는 input에 대한 정보를 알 수 없게 된다.

하지만 <label> 태그를 적어만 준다고 해결되는 것이 아니다.  
아래는 라벨과 input을 연결시켜주는 방법들이다.

```jsx
// 1. 암시적으로 맵핑
<form>
	<label> username : 
		<input type="text" />
	</label>
</form>


// 2. html 속성 이용하기 
// 리액트는 htmlFor, 일반 html은 for를 사용하여 맵핑시켜준다. 
<form>
	<label htmlFor="username">username :</label>
	<input id="username">
	<button type="submit">제출하기<button>
</form>

// 3. aria-labelledby
<form>
	<label id="username-label">username :</label>
	<input aria-labelledby="username-label">
	<button type="submit">제출하기<button>
</form>

// 4. aria-label
<form>
	<label>username :</label>
	<input aria-label="username" />
	<button type="submit">제출하기<button>
</form>

// 5. title attribute
<form>
	<label>username :</label>
	<input title="username" />
	<button type="submit">제출하기<button>
</form>
```

위의 방법 외에도 placeholder, aria-placeholder를 사용하는 방법 등 여러가지가 있으나, 아래에 두 가지 방법이 가장 권장된다.

웹 접근성은 스크린 리더를 사용하는 사용자만이 있는 것이 아니기 때문에, 3번과 4번을 사용하는 경우에는 스크린 리더를 사용하지 않는 사람에게 정보를 줄 수 없다.

그리고 placeholder의 경우, input 작성시 사라지기 때문에 작성시에는 해당 input에 대한 정보를 줄 수 없다.

- 암시적 바인딩
- for(or htmlFor)를 사용


## Input

Input 태그에는 여러가지 속성들이 있다,

`required`  : 해당 값이 submit에 필요한 데이터라는 것을 명시.  만약 해당 값이 채워지지 않은 경우에 `:invalid` css pseduo class가 활성화 된다.

```html
<form>
	<input type="text" required>
</form>
```

```css
input:invalid {
	background-color: red; 
} 
```

## 에러 처리

input에서 에러가 났을때, 이를 표현하는 방법은 대표적으로 3가지가 있다.


- 에러 다이얼로그
- 에러 영역 : form submit 시에 validation
- 인라인 에러 메시지 : onChange 시에 실시간으로 validation


### 에러 다이얼로그

다이얼로그의 특성상 다이얼로그는 기존의 화면과 별개의 영역으로 구분이 된다.

다이얼로그를 사용할 때는 여러가지를 유의 해야한다.

1. 에러 메세지를 다이얼로그에 표시하는 경우, 다이얼로그를 닫는 경우 해당 다이얼로그의 에러 메세지를 다시 볼 수 없음에 유의해야 한다.
2. 키보드 포커스 관리에 유의해야한다.
3. 이벤트 버블링에 유의해야한다.

> 참고로,  다이얼로그나, 툴팁, 로더 같은 것들을 만들때 ReactDOM의 createPortal을 통해 생성하는 것이 좋다. 컨테이너 내부에 위 요소를 생성하는 경우,  overflow 속성과 같은 것에 의해서 위젯이 가려질 여지가 있다.  그래서 position과 z-index를 통해 이를 우회해야하지만, createPortal을 사용하면 다른 상위의 node에 생성하기 때문에 이를 쉽게 구현할 수 있다.

예제를 위해 간단하게 만들었습니다.

1. 에러 모달에 대한 정보 제공

모달에 대한 essential한 정보는  `aria-label` or `aria-labelledby`을 통해서 전달해준다.  이 경우에는 해당 에러의 타입이 essential 한 정보이기 때문에 이를
`aria-label="Error Dialogue"`라고 표시해주거나, headings 태그에 이를 연결해주면 된다.
그리고 에러에 대한 메세지는 부가적인 정보로, 메세지에  `aria-describedby`를 통해 연결해줄 수 있다.


3. 키보드 포커스 관리

우선 해당 예제에는 확인 버튼만 있으면 되지만,  (확인, 취소) 와 같은 쌍의 버튼이 두가지가 있을때는, modal이 생성되었을때 버튼에 포커스가 가도록 하는 것이 좋다.

컴포넌트가 마운트 될 때, 실수로 누를 수도 있는 버튼이 아닌 버튼에 키보드 포커스를 주는 것이 좋다.

예를 들어, 구독, 취소하기와 같은 버튼이 있는 경우 해당 모달이 열릴 때, 포커스는 취소하기에 올라가 있는 것이 바람직하다.

```jsx
// Modal 컴포넌트
const Modal = ({ children }) => {
	const modalRoot = document.getElementById('modal-root');
	return modalRoot 
		&& ReactDOM.createPortal(children, modalRoot);
}

// ErrorDialouge 모달
const ErrorDialogue = ({ handleOK, handleClose, message }) => {
	const closeRef = useRef(null);
	useEffect(()=>{
		// 모달이 뜨는 순간 취소 버튼에 포커스를 준다.  	
		if(closeRef.current) {
			closeRef.current.focus();
		}
	},[])

	return 
		<Modal>
			<div 
				role="dialog" 
				aria-labelledby="dialogTitle" 
				aria-describedby="dialogDescription"
			>
  				<h2 id="dialog1Title">
					Error Dialogue
				</h2>
  				<p id="dialog1Desc">
					{message}
				</p>
				<button onClick={handleOK}>OK</button>
  				<button 
					ref={closeRef} 
					onClick={handleClose}
				>Close</button>
			</div>
		</Modal>);
}

```

```jsx
// 폼 컴포넌트 
const Form = () =>{ 

	const inputRef = useRef(null);
	
	const [input, onChangeInput, 
		errorMessage, setErrorMessage] = useInput('');
	const [isModalOpen, setIsModalOpen] = useState(false);

	const handleSubmit = useCallback((e)=>{ 
		e.preventDefault();
		if(validate.validateInput(input)) {
		// ... submit logic
		}else {
			handleInputError();
			setIsModalOpen(true);
		}
	}, [input]);

	const handleInputError = () => {
		setErrorMessage('message');
	} 

	const handleOK = () =>{ 
		// 생략...
	}

	// 만약 모달에서 닫기 버튼을 눌러서, 
	// 해당 메서드가 호출되면 에러가 난 input으로 다시 포커스를 올려준다.   
	const handleClose = () =>{  
		if(inputRef.current) {
			inputRef.current.focus();
		}
	}

	return (
		<section onSubmit={handleSubmit}>
		<form>
			<input 
				id="username"
				ref={inputRef} 
				value={input} 	
				onChange={onChangeInput} />
			<button type="submit">제출하기</div>
		</form>
		{errorMessage && 
			<ErrorDialogue 
				message={errorMessage} 
				inputRef={inputRef}
			/>}
		</section>
	)
}
```


### 에러 영역

form 태그 위나 아래에, 에러 메시지를 표현해주는 영역을 별도로 구분해주는 것이다.

해당 영역은 tab 키를 통해 이동하지 못하고,  프로그래밍으로만 포커스를 줄 수 있도록 설정하기 위해 `tabindex="-1"` 속성을 부여하였다.

또한, 에러 메세지 영역을 읽고 해당 에러가 난 인풋으로 이동하기 위해서 anchor를 걸어두는 것이 좋다.

```jsx
import * as React from 'react'; 
import validate from 'utils/validate';
const Form:React.FC = () => {


const errorRef = useRef(null);

const [input, onChangeInput, errorMessage, setErrorMessage] = useInput('');
const handleSubmit = useCallback((e)=>{ 
	e.preventDefault();
	if(validate.validateInput(input)) {
		// ... submit logic
	}else {
		handleInputError();
	}
},[input]);

const handleInputError = () => {
	setErrorMessage('username should be ...');
	if(errorRef.current) {
		errorRef.current.focus();
	}
} 
return (
	<>
		// Error area
		<section ref={errorRef} tabindex="-1" aria-labelledby="error-message">
			<h2 id="error-message">form data errors</h2>
			<ul>
				<li>
					<a href="#username">{errorMessage}</a>
				</li>
			</ul>
		</section>
		<form onSubmit={handleSubmit}>
			<input id="username" value={input} onChange={onChangeInput} />
			<button type="submit">제출하기</div>
		</form>
	</>)
} 
```


### 인라인 에러 메시지

인라인 에러 메시지는 실시간으로 onChange 메서드가 실행되면서 validation을 하는 방식이다.

이 경우에는 인풋 메세지에 포커싱을 주는데 유의해야한다. 왜냐하면, 매번의 타이핑마다, 에러 메세지에 포커싱을 주는 경우에 문제가 생길 수 있다.

이메일 form을 입력할때, 앞의 유저 이메일을 입력할 때, 도메인 입력 이전에는 `@` 가 없기 때문에 항시 에러 메세지가 나타날텐데, 타이핑마다 에러메시지로 포커싱을 준다면 사용자 경험에 있어서 문제가 생길 것이다.  따라서 이 경우에는, 포커스 관리를 유의깊게 해주어야 한다. 예를들어, blur 될때, submit 할 때, 에러메세지를 확인하고 포커스를 주어야한다.


```jsx
const Form = () => {

const [input, setInput] = useState('');
const [inputError, setInputError] = useState(null);
const onChangeInput = useCallback((e)=>{ 
	const value = e.target.value;
	onSetInput(value);
	cosnt [isValid, errorMessage] = useValidate(value);
	if(!isValid){
		setInputError(errorMessage);
	}
},[input])

return (
	<form> 
		<label for="email">Email</label>
		<input
	   		id="email"
			value={input}
			onChange={onChangeInput}
	   		aria-describedby="email-error"
		/>
		{inputError && 
			(<p id="email-error">
				{`Email address ${input} is invalid`}
			</p>)}
	</form>
)
```


#웹접근성