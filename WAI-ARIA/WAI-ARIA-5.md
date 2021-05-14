# 웹 접근성 고찰 6 - aria-*

## aria-label


The  [aria-label](https://www.w3.org/TR/wai-aria/#aria-label)  attribute is used to define a string that labels the current element.

Use it in cases where a text label is *not* visible on the screen.

If there is visible text labeling the element, use  [aria-labelledby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute)  instead.
This attribute can be used with any typical HTML element; it is not limited to elements that have an ARIA role assigned.


## aria-labelledby

`aria-labelledby` 는  DOM 객체에 대한 **핵심적인 설명** 을 전달해주는 역할을 한다.

aria-label은 string으로 직접 설명해주는 것과 달리, `aria-labelledby`는 이를 설명해주는 라벨과 연결해주는 역할을 해준다.

input 과  label 을  for / id 를 연결 시키는 것과 비슷하다.


```html
<div>
	<label for="foo" >비밀번호 : </label>
	<input id="foo" />
</div>
```


```html
<!-- div의 라벨은 foo id를 가진 tag가 된다. -->
<div aria-labelledby="foo"> 
	<tag id="foo"> </tag>
</div>
```

```html
<div role="main" aria-labelledby="foo">
   <h1 id="foo">Wild fires spread across the San Diego Hills</h1>
   Strong winds expand fires ignited by high temperatures ...
</div>
```

```html
<div role="menubar">
    <div role="menuitem" aria-haspopup="true" id="fileMenu">File</div>
    <div role="menu" aria-labelledby="fileMenu">
        <div role="menuitem">Open</div>
        <div role="menuitem">Save</div>
        <div role="menuitem">Save as ...</div>
        ...
    </div>
    ...
</div>

```

이렇게 연결을 시켜주면, 스크린 리더에서 목록을

The  [aria-labelledby](https://www.w3.org/TR/wai-aria/#aria-labelledby)  attribute establishes relationships between objects and their label(s), and its value should be one or more element IDs, which refer to elements that have the text needed for labeling. List multiple element IDs in a space delimited fashion.

This idea is similar to how the "for" attribute is used to link a LABEL to an INPUT.


Assistive technology, such as screen readers, use this attribute to catalog the objects in a document so that users can navigate between them. Without an element ID, the assistive technology cannot catalog the object.
aria-labelledby is very similar to  [aria-describedby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-describedby_attribute) : A label provides essential information about an object, while a description provides extended information that the user might need.
In addition to form elements, you can use the aria-labelledby attribute to associate static text with widgets, groups of elements, panes, regions that have a heading, definitions, and other types of objects. The  [Examples](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute#examples)  section below provides more information on how to use the attribute this way.

To improve compatibility with user agents that do not support ARIA, you can use aria-labelledby with the  [<label>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)  element (using the for attribute).

This attribute can be used with any typical HTML form element; it is not limited to elements that have an ARIA role assigned.


## aria-describedby

DOM 객체를 설명하기 위한 것으로 일반적으로 **부가적인 설명**이 필요한 경우에 사용한다.

웹 어플리케이션의 경우 자바스크립트 라이브러리를 통해 만들어진 위젯(tab, menu, calander 등)의 경우 이 위젯에 대한 의미 전달이 불분명하다.

`aria-describedby` 를 통해서 위젯과, 특정 섹션, element 그룹에 대한 부가적인 설명을 해줄 수 있다.

만약 탭 바를 만들어서 링크를 걸어주는 경우를 생각해보자.  아래와 같은 코드를 스타일링을 해주는 경우 스크린을 보고 해당 탭을 누르면 챕터를 이동할 수 있다는 것을 알 수 있다.

하지만, 스크린을 볼 수 없는 유저에게는 챕터 1, 2, 3 이라는 리스트와 아래의 글들이 나열될 뿐이다.

```html
<!-- before --> 
<ol>
  <li id="chapter1">
    <a href="#ch1Panel">챕터 1</a>
  </li>
  <li id="chapter2">
    <a href="#ch2Panel">챕터 2</a>
  </li>
  <li id="chapter3">
    <a href="#quizPanel">챕터 3</a>
  </li>
</ol>

<div>
  <div id="ch1Panel">Chapter 1 content goes here</div>
  <div id="ch2Panel">Chapter 2 content goes here</div>
  <div id="quizPanel">Quiz content goes here</div>
</div>

<!-- after -->
<div aria-describedby="descriptionTab"> 
	<h2>챕터 목록</h2>
	<div id="descriptionTab">This tab is linked to corresponding chapter<div> 
	<ol role="tablist">
  		<li id="chapter1" role="tab">
    		<a href="#ch1Panel">챕터 1</a>
  		</li>
  		<li id="chapter2" role="tab">
    		<a href="#ch2Panel">챕터 2</a>
 		</li>
 	 	<li id="chapter3" role="tab">
    		<a href="#quizPanel">챕터 3</a>
  		</li>
	</ol>
<div>
<div>
  <div id="ch1Panel" role="tabpanel" 
		aria-labelledby="chpater1">
		Chapter 1 content goes here
	</div>
  <div id="ch2Panel" role="tabpanel" 
		aria-labelledby="chpater2">
		Chapter 2 content goes here
	</div>
  <div id="quizPanel" role="tabpanel" 
		aria-labelledby="chpater3">
		Chapter 3 content goes here
	</div>
</div>
```


usage case
- input 태그에서  label 외에 부가적인 설명이 필요한 경우 (eg. 에러 메시지에 대한 설명)
- 특정 위젯에 대한 부가적인 설명 (eg. 해당하는 내용이 달력을 표시한다는 것에 대한 설명)
- 버튼의 기능에 대한 부가적인 설명 (eg. 다이얼로그 메세지의 확인 버튼 클릭시 발생하는 동작에 대한 부연설명)


## aria-hidden
#웹접근성/aria-label