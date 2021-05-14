# Flex

```html
<container>
	<item/>
	<item/>
	<item/>
	<item/>
</container>
```


inline-flex ?

외부의 다른 요소들과의 관계가 변한다

아래의 코드가  flex 로 적용되는 경우에는 hihi 라는 글귀가 아래로 떨어지지만,  inline-flex를 적용하는 경우에(만약 공간이 남는다면) 우측에 배치가 된다.

즉 컨테이너가 일종의 inline 처럼 동작하게 되는것

```html
<container>
	<item/>
	<item/>
	<item/>
</container>
hihi
```

flex-direction

- row
- row-reverse : 우측에서 좌측으로
- column
- column-reverse : 밑에서 부터 아래로

flex-wrap

- nowrap(default) : item이 컨테이너보다 커졌을때, item이 컨테이너 밖으로 나가게 된다.
- wrap : item이 컨테이너보다 커지는 경우, 아래로 떨어뜨려준다.
- wrap-reverse: 아래를 채우고 떨어지는게 아니라 위로 올라간다.



flex-flow
flex-direction 과 flex-wrap의  shorthand라고 볼 수 있다.

```css
.container {
	flex-flow: flex-direction flex-wrap
}
```


반응형

```css
.container {
	display: flex; 
	flex-direction : column;
	@media(min-width: 600px) {
		display: flex;
		flex-direction: row;
	}
}
```



## justify-content

- flex-start
- flex-end
- space-between
- space-around : 좌우 여백을 동일하게 가짐. 따라서, 아이템 사이 간격은 여백 2개
- space-evenly:  모든 여백이 동일한 경우


```css
.container {
	justify-content: 
	
}
```


## align-items
- stretch(default): 아이템이 수직축으로 쭉 늘어진다.
- flex-start:
- flex-end
- center
- baseline : 텍스트 베이스 라인을 기준으로 정렬한다.

align-content
flex-wrap 이 설정된 경우에는 아이템이 아래로 떨어지는데, 이 떨어진 아이템으로 인해서 행이 2줄 이상되었을때, 수직축을 결정해준다.
- stretch :  아래로 떨어지되 공간은  stretch 해서 채워준다.
- flex-start : 위에서 부터
- flex-end : 아래에서부터  아래로 떨어짐
- space-between:  중간에 여백이 생기고 아래로 떨어지는 아이템은 바닥에 붙는다
- space-around
- space-evenly

## flex-basis
flex-item 의 기본 크기를 설정 ( direction row -> 가로 폭,  column ->  세로 높이)

단위는 %, px, rem, em  등등의 수치 단위를 쓸 수 있다.

flex-basis : 000px 인 경우에는 000px 보다 아이템이 큰 경우에는 늘어난다
width: 000px 인 경우에는 강제로 맞춘다.

## flex-grow

flex-basis를 제외한 여백을 flex-grow에 지정된 숫자 비율대로 나누어 가져서 늘어나게 된다.

크기 자체가 비율로 나눠 갔는게 아니다.

## flex-shrink
default : 1

줄어들때, 비율대로 줄어든다.

flex-shrink: 0 인 경우에는 줄어들지 않는다.

## flex shorthand

flex : flex-grow flex-shrink flex-basis

```css
flex : 1
// flex: 1 1 0%


flex: 1 1 auto;

flex: 1 500px;
// flex: 1 1 500px
```

basis 를 생략하는 경우에는 전체 영역을 비율대로 나눠가진다.

하지만 아래와 같은 비율로 만든다고 하여도, item1 내부에 p 가 nowrap 속성을 가지고, 내부에 글자가 많은 경우에는 강제로 1: 2: 1 비율을 맞춰주지 않는다.

```css
.flex-item1 {
	flex: 1;
	.p {
		white-space: nowrap;
	}
}
.flex-item2 {
	flex: 2;
}
.flex-item3 {
	flex: 1;  
}
```


이 경우에는  container 내부의 아이템을 flex로 설정하는 것보다는 width: 00%를 사용하는 것이 더 좋다.
정확하게 나누는 다단 칼럼의 경우에는 flex를 사용하는것이 문제가 될 수 있다.


아이템 3개를 2: 6: 2 비율로 첫 줄을 채우고, 4번째 아이템을 두번째 줄로 떨어뜨리고 넓이를 꽉채우고 싶을때.

만약에 flex: 1; 을 사용하는 경우에는  flex-basis 가 0이 되어서, 4개의 아이템이 같은 크기를 가지게 된다.

따라서  flex: 1 1 auto; 를 사용하면 width를 퍼센트로 나누고, 다음 아이템은 아래로 떨어뜨리는데, flex: 1 1 auto; 때문에 아래 줄을 가득 채우게 된다.

```css
.container {
	display: flex;
	flex-wrap: wrap;
	.flex-item {
		flex: 1 1 auto;
	}
	.flex-item:nth-child(1) {
		width: 20%;
	}
	.flex-item:nth-child(2) {
		width: 60%;		

	}
	.flex-item:nth-child(3) {
		width: 20%;
	}
}
```

```html
<div class='container'>
	<div class='flex-item'>

	</div>
	<div class='flex-item'> 

	</div>
	<div class='flex-item'>

	</div>
	<div class='flex-item'>

	</div>
</div>
```

## 반응형 컬럼

width vs flex-basis

flex-basis <- auto 설정시 width를 취한다.

item이 3개가 있고, 이 아이템에  width: 30%를 했는데,  flex: 1 1 auto; 로 설정되어 있는 경우에는  flex-grow가 1이기 때문에 여백을 나누어 같게 되어, 아이템 3개는 3등분되어 버린다(이 경우에도 아마.. 컨텐트의 영향을 받아서 정확히 3등분은 안될듯?).




## margin-000:auto
item이 세개가 있을 경우 2개는 좌측에 1개는 우측으로 붙는다.

margin-auto는 남아있는 마진을 모두 소모한다. 따라서 마지막 아이템이 마진의 모두 소모해서 우측으로 붙는것


```css
.flex-container {
	display flex;
}
.flex-item {
	width: 150px
}
.flex-item:last-child {
	margin-left: auto;
}
```

## 고정폭 + 가변 폭

아래와 같이 하면 1번과 3번이 줄어들때 같이 줄어든다.  왜냐하면 flex-shrink 값의 default가 1이기 때문이다.

```css
.flex-container {
	display flex;
}
.flex-item:nth-child(1) {
	width: 150px
}
.flex-item:nth-child(3) {
	width: 150px
}
```


```css
.flex-container {
	display flex;
}
.flex-item {
	flex: 0 0 auto;
}
.flex-item:nth-child(1) {
	width: 150px
}
.flex-item:nth-child(2) {
	flex-grow: 1;
}
.flex-item:nth-child(3) {
	width: 150px
}
```

## 헤더 푸터

```html
<div class='page'>
	<header class='header'>

	</header>
	<main class='content'>

	</main>
	<footer class='footer'>

	</footer>
</div>
```

아래와 같이 설정하면 footer가 아래로 붙지 않는다. 왜냐하면  전체 page의 높이가 전체를 차지하지 않기 때문에.
```css
.page {
	display flex;
	flex-direction: column;
}
.content {
	flex: 1 1 auto;
}
```

```css
.page {
	display flex;
	flex-direction: column;
	min-height: 100vh;
}
.content {
	flex: 1 1 auto;
}
```

윈도우에서는 min-height:  100vh 만 설정해주면 버그 생김

아래 처럼하면 content 영역은 스크롤 생김


```css
.page {
	display flex;
	flex-direction: column;
	height: 100vh;
}
.content {
	flex: 1 1 auto;
	overflow: auto;
}
```


## align-self

이 속성은 각각의 아이템에 부여할 수 있는 속성이다.  속성의 값은  align-items 와 동일.

container의 align-items 보다 우선권을 가짐.


#CSS/Flex