## 브라우저의 기본 수행 동작

문서 내에 다양한 태그가 있을 때 브라우저는 각각의 태그에 대한 기본 동작을 수행한다.

예를 들어, `<a>` 태그를 클릭하면 해당 태그에 연결된 하이퍼링크로 이동하고, `<input>` 태그를 클릭하면 focus가 생기면서 키 입력을 할 수 있도록 커서가 깜빡이는 등이다.

## event.preventDefault();

이벤트 객체의 `preventDefault` 메서드는 이벤트의 기본 동작을 막는 메서드이다. 즉, 위에서 설명한 브라우저가 기본적으로 수행하는 동작들을 취소할 때 사용한다.

### 우클릭 막기

대표적으로는 웹 페이지 내에서 우클릭을 막는 행위를 예시로 들 수 있다.

우클릭 이벤트는 `contextmenu`라고 하는데, 이 이벤트에 대해 `preventDefault` 메서드를 적용하면 웹 페이지에서 우클릭을 못하게 할 수 있다.

```js
<p id='p'>text</p>

<script>
const myTag = document.getElementById('p');

function prevent(e) {
    e.preventDefault();
    alert('우클릭 금지');
}
myTag.addEventListener('contextmenu', prevent);
</script>
```

위 코드를 실행하게 되면, `<p>` 태그에다 우클릭을 하려고 하면 우클릭이 중지되면서 '우클릭 금지'라는 알림이 나타난다.

만약에 웹 페이지 전체에 우클릭 금지를 적용하고 싶으면 태그 대신 `document` 객체를 설정해주면 된다.

```html
<script>
  function prevent(e) {
    e.preventDefault();
    alert("우클릭 금지");
  }

  document.addEventListener("contextmenu", prevent);
</script>
```

### 다양한 활용
`preventDefault()`를 사용하면 다양한 방식으로 브라우저의 기본 동작을 제어할 수 있다.

#### a 태그 기본 동작 막기
```html
<a href="/" id="myLink">클릭해서 이동</a>

<script>
    const myLink = document.getElementById('myLink');
    function preventHandler(e) {
        e.preventDefault();
        alert('지금은 이동 불가합니다.')
    }
    myLink.addEventListener('click', preventHandler)
</script>
```
위 코드를 실행하면, `<a>` 태그를 클릭해도 이동하지 않고, '지금은 이동 불가합니다.' 알림이 뜬다.