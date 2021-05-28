# DOM
DOM(Document Object Model)은 웹의 도큐먼트의 구조와 콘텐츠를 구성하고 있는 자료구조이다.

DOM이라는 자료구조는 일종의 인터페이스로 프로그래밍 언어가 Document를 다룰 수 있도록 해준다. DOM은 아래로 구성된다.

- node
- property
- method

## Node
HTML의 요소(element)는 HTML 문서를 구성하는 각 개별 요소이다. 
HTML 문서가 렌더링 엔진에 의해 파싱되면서 DOM 객체로 변환될 때, 이 HTML element는 노드 객체로 변환된다.

> elements !== nodes
>

```html
<div class="foo">boo</div>

<!-- 
div 
class="foo"
boo
-->
```

노드 객체의 종류(타입)가 여러가지가 있는데, 4개의 중요한 타입이 있다.

- document
  DOM 트리의 최상단에 위치하는 루트 노드. 전역객체 window에 document 프로퍼티로 바인딩 되어 있다.
- element
  HTML element를 가리키는 객체. element가 nesting에 의해서 부자 관계를 가지는 것처럼 element 노드 또한 부자 관계를 가지고 있다. 즉, 이러한 관계를 통해서 문서의 구조를 나타낸다.
- attribute
  HTML element가 가지는 속성(attribute)를 가리키니는 객체. attribute 노드는 부자관계를 가지지 않고, element 노드와 형제 관계를 가지지도 않는다. 따라서 element 요소에 접근후 attribute 노드를 접근해야한다.
- text node
  HTML 요소의 텍스트를 가리키는 객체. element 노드의 자식 노드이며, leaf 노드이다.



#javascript