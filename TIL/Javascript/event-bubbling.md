## 이벤트 버블링이란?
이벤트 버블링은 하나의 요소에서 발생한 이벤트가 해당 요소에서 시작해 부모 요소로 전달되는 현상을 말한다.

이벤트가 자식 요소에서 발생하면, 해당 자식 요소에 할당된 이벤트 핸들러가 먼저 실행되고, 그 후 부모 요소로 이벤트가 전달된다. 이때 부모 요소에도 동일한 타입의 이벤트 핸들러가 등록되어 있으면 부모 요소에서도 이벤트 핸들러가 실행된다.

이벤트 버블링은 **자손 → 자식 → 부모 → 조상 순**으로 진행되고, 최상단의 `window` 객체에 도달할 때까지 계속해서 이벤트가 전파된다.

내가 헷갈렸던 부분은, 자식 요소에서 부모 요소로 이벤트 핸들러가 전달되는 게 아니고 **발생한 이벤트 그 자체만 전달된다**는 것이었다.

### 코드 예시
```js
const container = document.querySelector('.container');
const list = document.querySelector('.list');
const items = document.querySelectorAll('.item');

container.addEventListener('click', function(e) {
    console.log('container event');
});

list.addEventListener('click', function(e) {
    console.log('list event');
});

for (let item of items) {
    item.addEventListener('click', function(e) {
        console.log('item event');
    });
}
```

위 코드에서 `container`, `list`, `item` 요소들에 모두 ‘click’ 이벤트 핸들러가 등록되어 있다.
- `item` 요소를 클릭하면 ‘item event’가 먼저 출력되고,
- 이어서 이벤트 버블링에 의해 ‘list event’, ‘container event’ 순으로 출력된다.

### 이벤트 핸들러가 없는 경우

만약 다음과 같이 `item` 요소들에는 아무 이벤트 핸들러를 붙여주지 않는다고 한다면 어떻게 될까?
```js
const container = document.querySelector('.container');
const list = document.querySelector('.list');
const items = document.querySelectorAll('.item');

container.addEventListener('click', function(e) {
    console.log('container event');
});

list.addEventListener('click', function(e) {
    console.log('list event');
});

// for (let item of items) {
//     item.addEventListener('click', function(e) {
//         console.log('item event');
//     });
// }
```
이 경우에도 이벤트 버블링이 발생한다. `item` 요소에서 발생한 클릭 이벤트가 `list`와 `container`로 전파되기 때문. 이벤트 버블링은 이벤트 핸들러의 여부와 관계 없이 이벤트 자체를 부모 요소로 전파한다.

## `target` vs `currentTarget`

이벤트가 발생한 요소를 나타내는 이벤트 객체 프로퍼티인 `target`과 `currentTarget`을 비교해보자.

### `target`
- 이벤트가 발생한 실제 요소 참조
- 클릭 이벤트가 `button`에서 발생했다면 `target`은 `button` 요소를 가리킨다.
- 이벤트 버블링이 일어나도 이벤트 객체의 `target` 프로퍼티는 변하지 않고 처음 이벤트가 발생한 요소를 담고있다.

### `currentTarget`
- 이벤트 핸들러가 현재 실행중인 요소 참조
- 이벤트 버블링이 진행되면서 같은 타입의 이벤트에 할당된 이벤트 핸들러가 할당된 요소를 가리킨다.
- 즉, 버블링이 진행되면서 현재 실행 중인 핸들러가 할당된 요소가 반환된다.

```html
<div class='container'>
    <button class='button'>Button</button>
</div>
```
```js
const container = document.querySelector('.container');
const button = document.querySelector('.button');

const containerHandler = (e) => {
    console.log('target:', e.target)
    console.log('currentTarget:', e.currentTarget)
}
const buttonHandler = (e) => {
    console.log('target:', e.target)
    console.log('currentTarget:', e.currentTarget)
}

container.addEventListener('click', containerHandler);
button.addEventListener('click', buttonHandler);
```
위와 같은 코드가 있을 때,

1. `button`을 클릭하면 `e.target`은 `button` DOM 요소를 가리킨다.
`e.currentTarget`은 `button` 요소이다.

2. 이벤트 버블링이 되면서, `container` 요소에 이벤트가 전달되어도 `e.target`은 `button`이고, `e.currentTarget`은 `container`가 된다.

## 버블링 중단하기
이벤트 버블링은 타겟 이벤트에서 시작해서 `<html>` 요소를 거쳐 `document` 객체를 만날 때까지 각 노드에서 모두 발생한다. 몇몇 이벤트는 `window` 객체까지 거슬러 올라가기도 한다. 이때에도 모든 이벤트 핸들러가 호출된다.

그런데 핸들러가 이벤트를 완전히 처리하고 난 후 버블링을 중단하도록 명령할 수도 있다.

이벤트 객체의 메서드 `event.stopPropagation()`을 사용하면 해당 이벤트 핸들러가 동작한 후 이벤트 버블링이 일어나지 않는다.

```js
<div onclick="alert(`버블링은 여기까지 도달하지 못합니다.`)">
  <button onclick="event.stopPropagation()">클릭해 주세요.</button>
</div>
```

> ⚠️ 메서드 하나로 버블링을 막을 수 있지만, 정말 필요한 경우가 아니라면 가급적 버블링을 막는 일은 피하는 것이 좋다. 아키텍처를 잘 고려해서 정말 막아야 하는 상황에서만 막아야 한다.
> 
> `stopPropagation`을 사용하여 버블링을 막아버린 요소의 영역은 **죽은 영역(Dead Zone)**이 되어버린다. 이렇게 되면 `document`나 `<body>`에 걸친 이벤트(클릭 패턴 수집 등)를 적용할 때, 해당 영역은 이용할 수 없게 되어버린다.

## 참고 문헌
- 코드잇 '이벤트 버블링'
- [버블링과 캡처링](https://ko.javascript.info/bubbling-and-capturing)