## 이벤트 위임 (Event Delegation)

이벤트 위임은 **부모 요소에 이벤트 리스너를 할당하여 자식 요소들의 이벤트를 처리**하는 기법이다. 이벤트 버블링의 원리를 이용하는 것이다.

보통은 각 요소마다 개별적으로 이벤트 리스너를 추가하지만, 이벤트 위임을 사용하면 부모 요소 하나만으로 리스너를 가지고 자식 요소들의 이벤트를 감지할 수 있다.

## 이벤트 위임이 필요한 이유

### 1. 새로운 요소가 동적으로 추가될 경우

- `document.createElement()`로 새로운 요소를 추가하면 기존 이벤트 리스너가 적용되지 않음
- 이벤트 위임을 사용하면 새로 추가된 요소도 이벤트를 감지할 수 있음

### 2. 성능 최적화

- 각 요소마다 리스너를 붙이면 메모리 사용량이 증가
- 부모 요소 하나에만 리스너 등록하면 전체적 성능 향상

### 개별 요소에 이벤트 추가한 경우
자식 요소들에 개별적으로 이벤트 리스너를 등록하면, 추후에 동적으로 추가된 요소는 이벤트 리스너를 또 개별적으로 할당해줘야 한다는 문제가 생긴다.

```js
const list = document.querySelector("#list");
const items = list.children;

items.forEach((item) => {
  item.addEventListener("click", function () {
    console.log("click");
  });
});

// 새로 추가한 요소에는 이벤트 리스너가 등록되지 않음!
const li = document.createElement("li");
li.classList.add("item");
li.textContent = "새로 추가한 요소";
list.append(li);
```

### 이벤트 위임 사용하기
자식 요소들 각각에 이벤트 리스너를 할당하지 않고, 부모 요소 하나에만 이벤트 리스너를 할당하면 이벤트 버블링에 의해 자식 요소의 이벤트도 감지할 수 있다. 
```js
const list = document.querySelector('#list');

list.addEventListener('click', function() {
    console.log('click');
});

// 동적으로 추가된 요소도 이벤트 핸들러가 적용된다.
const li = document.createElement("li");
li.classList.add("item");
li.textContent = "새로 추가한 요소";
list.append(li);
```

## 부모 요소 이벤트 핸들러 관리
이벤트 위임을 사용하게 되면 부모 요소에 이벤트 리스너를 할당하는 것이기 때문에, 당연히 부모 요소에 이벤트가 발생했을 때에도 이벤트 핸들러가 동작한다.

그래서 이벤트 위임을 사용할 때에는 원하는 요소에서 의도한 동작이 일어나게끔 따로 처리를 해줘야 한다.

```js
const list = document.querySelector('#list');

list.addEventListener('click', function(e) {
    // if (e.target.tagName === 'LI')
    if (e.target.classList.contains('item')) {
        console.log('click');
    }
});
```
위 코드 처럼 `target` 프로퍼티의 태그 네임을 설정하거나, CSS 식별자로 확실하게 구분할 수가 있다.

따라서 부모 요소에 이벤트가 전달이 되어도 이벤트 핸들러는 동작하지 않게 할 수 있다.

> ⚠️ 이벤트 위임은 이벤트 버블링을 이용한 기법이기 때문에, 당연히 자식 요소에서 `event.stopPropagation()`를 사용해버리면 이벤트 위임이 발생하지 않는다.