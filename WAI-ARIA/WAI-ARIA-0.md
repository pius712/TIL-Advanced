# 메모

- semantic markup
- WAI-ARIA

## Semantic markup
[HTML: A good basis for accessibility - Learn web development | MDN](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML#good_semantics)

HTML elements for their intended purpose

**1.** **Easier to develop with** — as mentioned above, you get some functionality for free, plus it is arguably easier to understand.
**2.** **Better on mobile** — semantic HTML is arguably lighter in file size than non-semantic spaghetti code, and easier to make responsive.
**3.** **Good for SEO** — search engines give more importance to keywords inside headings, links, etc. than keywords included in non-semantic <div>s, etc., so your documents will be more findable by customers.


*  [<article>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/article)
*  [<aside>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/aside)
*  [<figcaption>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/figcaption)
*  [<figure>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/figure)
*  [<footer>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/footer)
*  [<header>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/header)
*  [<main>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/main)
*  [<nav>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/nav)
*  [<section>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/section)
*  [<time>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time)



## WAI-ARIA
[Using ARIA: Roles, states, and properties - Accessibility | MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques#roles)
### role

웹에서 일반적으로 사용되는 content block **landmark roles**
-> 구조 파악에 도움이 됨.

[WAI-ARIA Roles - Accessibility | MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles)

* role=”banner”
* role=”navigation” (e.g., a menu)
* role=”main” (the main content of the page)
* role=”complementary” (e.g., a sidebar)
* role=”contentinfo” (meta data about the page, e.g., a copyright statement)
* role=”search”
* role=”form” -> html form 사용 권고
* role=”application” (a web application with its own keyboard interface)

그 외에도 주로 사용되는 UI에서 사용됩

- role='tab'
- role='dialog'
- role='alert-dialog'

-


### properties & state

markup에 추가적인 의미 전달 필요시 사용.
markup을 줄 수 없는 상황, 혹은 부가적으로 더 설명해야 하는 경우에 사용.

*  [aria-labelledby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute)
*  [aria-describedby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-describedby_attribute)
*  [aria-label](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-label_attribute)
*  [aria-required](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-required_attribute)
- aria-selected
- aria-hidden
- aria-live="token


element의 상태 정보를 나타내어 준다.
여러 가지 탭이 있을때, 해당 탭이 선택되었는가 아닌가
input이 disable인가 아닌가 등에 대한.

app의 life cycle 내에서
#웹접근성