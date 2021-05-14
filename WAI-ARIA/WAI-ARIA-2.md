#  웹 접근성 고찰 2 - Text Content


## List
- <ul>, <ol>, <li>  
- <dl> <dt> <dd>


### <dl>, <dt>, <dd>


[dl태그 설명 출처: w3.org](https://www.w3.org/TR/html50/grouping-content.html#the-dl-element)

`<dl>` 태그의 사용을 고려한다면,  **name-value** 에 대해서 생각해보아야 한다.

<ul>태그와 <li>태그는 리스트에 대한 나열인 반면,  dl, dt, dd는 name-value의 틀을 가지는 리스트들에 대한 그룹을 지을때 사용한다. 

저자와 공저, 번역에 대한 리스트를 작성할 때를 가정해보자.
이 경우, key - value 구조로 리스트가 생기게 된다.


```html
<!-- 
저자 - A
공저 - B
번역 - C -->
<dl>
	<dt>저자</dt>
	<dd>A</dd>
	<dt>공저</dt>
	<dd>B</dd>
	<dt>번역</dt>
	<dd>C</dd>
</dl>
```


혹은 특정 포스트에 대한 메타 데이터를 정렬하여 표현하고 싶은 경우에도 사용이 가능하다.

```html
<dl>
 <dt> 최근 수정일 </dt>
 <dd> 2004-12-23T23:33Z </dd>
 <dt> 업데이트 주기</dt>
 <dd> 60s </dd>
 <dt> 저자 </dt>
 <dt> 에디터 </dt>
 <dd> Robert Rothman </dd>
 <dd> Daniel Jackson </dd>
</dl>

```

****

#웹접근성