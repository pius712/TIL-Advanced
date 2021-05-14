# flex-ui

## menu


```html
<ul class="menu">
    <li class="menu-item">
        <a class="menu-link"></a>
    </li>
    <li class="menu-item">
        <a class="menu-link"></a>
    </li>
    <li class="menu-item">
        <a class="menu-link"></a>
    </li>
</ul>
```

```css
.menu {
    display: flex;
}
.menu-item{
    width: 25%;
    /*flex-grow: 1;*/
    background-color: gold;
}
.menu-item:hover {
    width: 35%;

    /*flex-grow: 1.25;*/
    background-color: crimson;
}
.menu-link {
    display: block;
    padding: 1em;
    font-size: 1.2rem;
    font-weight: bold;
    color : #555;
    text-decoration: none;
    text-align: center;
}
.menu-link:hover {
    color: white;
}



```


## 검색창
```html

    <form class="search-form">
        <input type="search" />
        <input type="submit" value="제" />
    </form>

```

```css
.search-form {
    display: flex;
    height: 40px;
}

.search-form input[type='search'] {
    flex: 1;
    margin-right: 10px;
    border: none;
    border-radius: 4px;
    font-size: 1rem;
}


.search-form input[type='submit'] {
    width: 4em;
    border: none;
    border-radius: 4px;
    font-size: 1rem;
}

```


## 불릿 리스트

```html

    <ul class="info-list">
        <li class="info-list-item">
            lorem
        </li>
        <li class="info-list-item">
            lorem
        </li>
        <li class="info-list-item">
            lorem
        </li>
    </ul>

```
```css

.info-list-item {
    display: flex;
    margin: 0.5em 0;
}
.info-list-item:before{
    content: '이모지'
    margin-right: 0.5em;
}

```

## 프로필 (아바타)



## 유저리스트


## Modal

```html

    <div class="modal">
        <div class="dialog">
            
        </div>
    </div>

```
```css
.modal {
    display:flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    left: 0;
    right:0;
    top:0;
    bottom:0;
    background-color: #555;
}

.dialog {
    background-color : #fff;
    width: 50vw;
    padding: 2em;
    border-radius: 16px;
}

```


## 카드리스트

```html
<ul class="card-list">
    <li class="card-item">
        <figure class="card-image">
            <img src="./iu.jpg" alt="."/>
        </figure>
        <div class="desc">
            lorem
        </div>
    </li>
    <li class="card-item">
        <figure class="card-image">
            <img src="./iu.jpg" alt="."/>
        </figure>
        <div class="desc">
            lorem
        </div>
    </li>
    <li class="card-item">
        <figure class="card-image">
            <img src="./iu.jpg" alt="."/>
        </figure>
        <div class="card-desc">lorem</div>
    </li>
    <li class="card-item">
        <figure class="card-image">
            <img src="./iu.jpg" alt="."/>
        </figure>
        <div class="card-desc">lorem</div>
    </li>
    <li class="card-item">
        <figure class="card-image">
            <img src="./iu.jpg" alt="."/>
        </figure>
        <div class="card-desc">lorem</div>
    </li>
    <li class="card-item">
        <figure class="card-image">
            <img src="./iu.jpg" alt="."/>
        </figure>
        <div class="card-desc">lorem</div>
    </li>
</ul>

```

```css
// reset css 적용 
ul {
    list-style: none;
}
.card-list {
    display: flex;
}

.card-item {
    display: flex;
    flex-direction: column;
    margin-bottom: 2rem;
}

.card-image {
    height: 0px;
    padding-bottom: 60%;
    background-color: lightgray;
    background-repeat: no-repeat;
    background-position: center;
    background-size: cover;
}

.card-image img{
    display:none;
}
.card-desc{
    flex: 1 1 auto;
    padding: 1em;
    background-color: white;
}

@media(min-width: 600px){
    .card-list {
        display: flex;
        flex-wrap: wrap;
        margin: 0 -1rem;
        /*justify-content: space-between;*/

    }
    .card-item {
        width: calc(50% - 1rem * 2);
        padding: 0 1rem;
    }
}
@media(min-width: 1200px){
    .card-list {
        display: flex;
        flex-wrap: wrap;
        margin: 0 -1rem;
        /*justify-content: space-between;*/

    }
    .card-item {
        width: calc(33% - 1rem * 2);
        padding: 0 1rem;
    }
}

```
#CSS/flex-ui