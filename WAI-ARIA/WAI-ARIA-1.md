# 웹 접근성 고찰 1 - Content Sectioning


## TLDL
`div` : **flow content**를  구분하는 단순한 컨테이너로 아무런 의미를 가지지 못한다.

`article` : 그 자체로 결합도를 가진 영역으로, 독립적으로 배포가능하고, 재사용가능한 영역에서 사용할 수 있다.
`section` : 독립적인 단위로 다른 곳과 구획을 나눌때 사용된다.

스크린 리더와 같은 보조기구에서는 문서의 구조를  headings(<h1>, <h2>...)을 기준으로 잡는다.
그렇기 때문에, <section>과 같은 semantic 태그에서 사용하는 headings는 보조기구에서 문서의 구조를 표현할때, 예상과 다르게 동작할 수 있다.

그럼에도 불구하고, semantic 태그는 웹접근성 측면에서 보조기구를 사용하는 사용자에게 도움을 줄 수 있다.



## <div>
### definition

**The HTML Content Division element (<div>) is the generic container for flow content.**

`<div>` 태그는 가장 오남용되고 있는 html 태그가 아닐까라고 생각을 한다.
mdn 문서 정의에서 div는 flow content를 구분하는 일반적인 컨테이너라고 한다.

### flow

그렇다면 flow란 무엇인가?

html은 태생이 문서에서 시작되었고, 이 문서는 작성되는 html의 특정한 흐름에 따라 파싱되어 생성된다.  CSS 스타일을 통해서 position과 같은 속성을 변경하지 않는다면 html은 normal flow를 따르게 된다.

### div usage case

div 태그는 이러한 flow를 구분하기 위한 것으로,  그 자체로는 아무런 의미가 없다.  
div가 사용되는  케이스에 대해 정리하면 아래와 같다.

- article, nav, button 등의 다른 적절한 semantic element 도저히 없을때.
- 특정 부분을 wrapping하여 style을 넣어주기 위해 구분할때.


## div를 semantic 하게 바꾸자.

- Container
- List

## Container
### section
도큐먼트에서 일반적으로 독립적인 파트를 나타낼때 사용한다.

이를 나타낼 수 있는 더 자세한 element가 없는 경우에 한하여 `<section>`을 사용해야한다.

```html
<section>
	<h2> 머릿말 </h2>
	<p> 문단 </p>
</section>
```

Usage case

- 컨텐츠가 `<main>`이 아닌 부가적인 역할을 하는 경우에는 `<aside>`를 사용한다.
- 컨텐츠가 주요한 컨텐츠 영역을 나타내면 `<main>` 태그를 사용한다.
- element를 스타일링하는 래퍼로 사용할때는 `<div>`를 사용한다.


### article

단독으로 배포가능한, 재사용이 가능한 단위일 때 사용한다. 이러한 예는 신디케이션이 있다.


> 참고) 신디케이션이란, 특정 앱에서 다른 앱으로 컨텐츠를 이용할 수 있도록 하는 방식인데,  검색엔진에서 신디케이션을 사용하면, 타 사이트에서 이를 크롤링하지 않고 등록되어 있는 정보를 통해서 표현해주는 기술.


블로그의 포스트, 뉴스나 매거진의 기사, 댓글 공간과 같은 곳이 article 태그의 대표적인 용례이다.

section과 마찬가지로, 특정 article 태그에는 heading을 달아주어서 해당 article을 구분할 수 있도록 해주는 것이 좋다.

Usage

- <article>은 중첩되어 사용될 수 있는데, outer와 inner는 서로 관련이 있는 경우여야 한다.  예를들어, 특정 포스트에 대한 댓글 영역에 댓글은 article로 작성할 수 있다. 

```html
<article>
	<h2>Div태그 이제 그만..</h2>
	<section> 
		<h3>div란 무엇인가??/</h3>
		<p>블라블라..</p>
	</section>
	<section>
		<h3>댓글</h3>	
		<article>
			<h4>질문있습니다</h4>
			<p>글쓴이께서 실무에서 이 원리를 철저하게 지키시나요?</p>
			<time>Jan 1</time>
		</article>
	</section>
</article>
```


## section vs article

article은 위에서 언급한 바와 같이 **단독으로 배포가능한, 재사용이 가능한 단위일 때, 사용한다. **  반면, section은 의미론적으로 구획을 나누는 역할을 하는 것 이상의 역할을 하지 않는다.

그렇기 때문에, 재사용이 가능하고, 그 자체로 구분이 가능한 결합체라면 <article>을 사용하고, 그 외의 경우에는 <section>을 사용하면 된다.

## headings와 section

스크린 리더와 같은 보조 기구를 사용하게 되면, document의 구조를 나타내주게 된다.

```html
<h1>1</h1>
<h2>2</h2>
<h2>3</h2> 
<h3>4</h3>
<h3>5</h3>
<h3>6</h3>
```

예를들어, 위와 같은 구조의 document가 있을 때, 보조기구나 브라우저의 접근성 분석도구에서는  headings를 기준으로 outline을 잡기때문에,  아래와 같은 구조로 outline이 잡히게 된다.

```html
-> 1
-> -> 2
-> -> 3
-> -> -> 4 
-> -> -> 5
-> -> -> 6
```


`<div>` 태그는 단순히 flow 상에서 구획을 나눠주는 역할을 한다면,  `<section>` 태그는 headings를 기준으로 구획을 나누어주는 역할을 한다고 설명을 했었다.

하지만 outline을 기준으로 생각해보면 section은 단순히 구획을 나누는 수준에 지나지 않는다.

접근성 관점에서 headings의 계층이 있고, 이 계층에 따라 화면상에는 구분이 되어있는것 처럼 보인다. 하지만, 이는 접근성 관점에서

만약 아래와 같은 구조의 html 문서가 있다고 가정하자.

```html
<body>
 <h1>헤더</h1>
 <section>
 	<h1>헤더2</h1>
 	<section>
  		<h1>헤더3</h1>
  </section>
 </section>
</body>
```

이렇게 구조가 잡혀있을 경우, 일반적으로는 아래와 같은 방식으로 outline이 생성되기를 원할지도 모른다.

```
-> 헤더1
-> -> 헤더2
-> -> -> 헤더3
```

하지만, 이러한 기대와 달리 헤더를 기준으로 아웃라인을 잡기 때문에 아래와 같은 구조를 띄운다.  <section> 태그를 작성한다고 하여도,  h1 태그가 가지는  `aria-level`을 가지기 때문에, 사실상 크게 의미가 없는 것이다.

```
-> 헤더1
-> 헤더2
-> 헤더3
```


공식적으로는 structural outline을 표시해주는 알고리즘이  구현되지 않았었기 때문에, HTML5가 나온 이후에도, 여전히 대다수의 브라우저, 스크린 리더는 document의 구조를 headings를 기준으로 구조를 보여주는 탓에 <section> 태그는 (<article>태그도 마찬가지로 semantic 태그에 불구하기 때문에 마찬가지다.)  사실상 접근성 관점에서 큰 의미를 갖지 못했다.

> 참고) outline을 잡아주는 알고리즘자체가 HTML5의 공식 스펙의 일부가 아니었다.


### 그렇다면, 기존처럼 headings를 기준으로 나누고 section은 사용하지 않아도 되는 것인가?

그것은 아니다.  문서의 구조를 표현하는 것은 headings를 기준으로 하지만, semantic element를 쓰는 것은 이것 외에도 장점이 많다.

예를들어, <nav> 태그를 사용하는 경우  탐색 영역과 컨텐츠 영역을 구분지을 수 있는데, 이는 스크린 리더 사용자에게 도움을 준다.  
이와 같이 <section> 태그의 경우 스크린리더가 이 요소에 대한 별도의 언급을 하지는 않지만, 의미론적으로 다른 영역과 별개의 섹션이라는 것을 표현할 수 있기 때문에, <div>를 사용하는 것보다 더 낫다.
보다 의미전달이 필요하여 별도의 언급이 필요할 때는,  `aria-label`을 통해서 표현하면, 이를 별도로 전달해줄 수 있다.

```html
<section aria-label="요약설명">
```


## Conclusion

> The power of the Web is in its universality. Access by everyone regardless of disability is an essential aspect.
> - Tim Berners-Lee, W3C Director and inventor of the World Wide Web







#웹접근성/Semantic  