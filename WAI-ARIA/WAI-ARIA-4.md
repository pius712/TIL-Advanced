# 웹 접근성 고찰 5 - role
role은 크게 아래의 범주로 나뉘어진다.

- Widget roles
- Composite roles
- Document structure roles
- Landmark roles
- Live Region roles
- Window roles

하지만,  위의 범주 아래에 있는 모든 role을 사용하는 것은 아니기 때문에, 자주 사용하는 것을 위주로 정리를...

제일 중요한 것은 aria를 사용하기 전에 html 태그 자체를 잘 써야한다.
예를 들어, 아래와 같이 `div` 태그에 `role=button`으로 설정하는 것 보다는 `<button>` 태그를 사용하는 것이 옳다.

기능적으로도, button의 경우에는 스페이스바를 눌러도 엔터가 눌러지는 반면, div에 role을 달아도 이런 동작은 프로그래밍해주어야한다. 그리고 form 태그내에서, `<button type="submit"`을 작성하는 경우에는 implicit submission이 동작하는 반면에, `<div role="button">` 으로 작성한다고 하여도 이는 동작하지 않는다.

따라서, aria-role 을 사용을 우선 고려하기 보다는,  html을 충분히 semantic하게 정해야한다.

```jsx
<div onClick={handleClick} role="button">버튼</div>
<button>버튼</button>
``` 


## 탐색 / 탭 목록


Navigation : 내비게이션 메뉴들은 탭과 달리 의미상 확연히 다른 페이지로 이동할 것이라 예측. 메뉴 간 연속성이 있으면 오히려 혼란의 가능성 야기. 아이콘 형태의 경우 관습성을 이용하는 것을 권장.

Tab : 서로 관련 있는 것들끼리의 논리적 묶음. 유저는 관습상 탭 내 다른 메뉴를 클릭하면 페이지는 전환되지만 의미상 유사한 페이지로 이동할 것이라 예측. 탭에서는 메뉴 간 연속성이 중요하기 때문에 만들어낸 신조어로 탭명을 사용하는 것은 지양.


##  탐색

The  [aria-labelledby](https://www.w3.org/TR/wai-aria/#aria-labelledby)  attribute establishes relationships between objects and their label(s), and its value should be one or more element IDs, which refer to elements that have the text needed for labeling. List multiple element IDs in a space delimited fashion.


### <nav>
<nav>   태그는 네비게이션 링크 집합이 있을 때 사용한다. 

`crumb` 같은 링크 계층을 만들때, 사용하면 좋다.

<nav>  링크를 사용하면 탐색 영역과 컨텐츠 영역을 구분지을 수 있는데, 스크린 리더 사용자에게 도움을 준다.  


* 문서의 모든 링크가 <nav> 요소 안에 있을 필요는 없습니다. <nav> 요소는 주요 탐색 링크 블록을 위한 요소입니다. 대개  [<footer>](https://developer.mozilla.org/ko/docs/Web/HTML/Element/footer)  요소가 <nav>에 들어가지 않아도 되는 링크를 포함합니다.
* <nav> 하나는 사이트 전체 탐색, 다른 하나는 현재 페이지 내 탐색으로 사용하는 등, 하나의 문서에서 여러 개의  [<nav>](https://developer.mozilla.org/ko/docs/Web/HTML/Element/nav)  태그를 가질 수 있습니다. 이럴 때  [aria-labelledby (en-US)](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute) 를 사용해 접근성을 향상할 수 있습니다.



```html
<nav>
	<ul>
		<li>Pull reqeust</li>
		<li>Issues</li>
		<li>Market Place</li>
	</ul>
<nav>

```

The <nav> tag defines a set of navigation links.
Notice that NOT all links of a document should be inside a <nav> element. The <nav> element is intended only for major block of navigation links.
Browsers, such as screen readers for disabled users, can use this element to determine whether to omit the initial rendering of this content.


##  Aria tab

`Aria tab`  은 탭 리스트를 나타낸다.


```html 
<div role="tablist" aria-label="Sample Tabs">
    <button role="tab" aria-selected="true" aria-controls="panel-1" id="tab-1" tabindex="0">
          First Tab
    </button>
    <button role="tab" aria-selected="false" aria-controls="panel-2" id="tab-2" tabindex="-1">
          Second Tab
     </button>
    <button role="tab" aria-selected="false" aria-controls="panel-3" id="tab-3" tabindex="-1">
          Third Tab
     </button>
</div>
``` 

탭`role="tab"`이란 해당 탭을 눌렀을 때,  그 탭에 활성화된
탭 패널  `aria-controls` 이 나오는 경우에 사용된다.
이러한 탭의 리스트를  탭 리스트 `role="tablist"`로 묶어준다.

일반적인 ux 패턴에서 탭을 누를 경우, 눌러진 탭은 다른 탭과는 확연히 구분되도록 해야한다.

`role="tab"`  을 사용할때, 지켜야할 사항

1.  role tab은 무조건  tablist role 의 자식이어야 한다.
2.  tab은 tabpanel  역할을 해주는  `aria-controls`  프로퍼티를 가져야한다.
3. tab role을 사용할때, 특정 탭이 선택된 경우에는 해당 탭의 `aria-selected` 속성 값이  `true` 가 되어야하고, 반대의 경우에는  `false` 가 되어야 한다.



They *should* contain the aria-controls property identifying an element with the tabpanel role.

An element with the tab role controls the visibility of an associated element with the tabpanel role. The common user experience pattern is a group of visual tabs above, or to the side of, a content area, and selecting a different tab changes the content and makes the selected tab more prominent than the other tabs.
Elements with the role tab *must* either be a child of an element with the tablist role, or have their id part of the aria-owns property of a tablist. This combination identifies to assistive technology that the element is part of a group of related elements. Some assistive technology will provide a count of the number of tab role elements inside a tablist, and inform users of which tab they currently have targeted. They *should* contain the aria-controls property identifying an element with the tabpanel role.

When an element with the tabpanel role has focus, or a child of it has focus, that indicates that the connected element with the tab role is the active tab in a tablist.

When working with elements with the tab role, when they are selected or active, they should have their aria-selected attribute set to true, otherwise it should be set to false. When a tab is selected or active, its controlled tabpanel should have its aria-expanded attribute set to true and its hidden attribute set to false, otherwise the reverse.


The ARIA tab role indicates an interactive element inside a tablist that, when activated, displays its associated  [tabpanel](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/Tabpanel_Role) .



#웹접근성