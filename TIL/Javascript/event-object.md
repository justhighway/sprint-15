## event와 event object
웹 페이지에서 발생하는 대부분의 이벤트는 마우스 이벤트와 키보드 이벤트로 나눌 수 있다.
- 마우스 이벤트의 대표적인 예: 'click'
- 키보드 이벤트의 대표적인 예: 'keydown'

다음과 같이 요소에 이벤트와 이벤트 핸들러를 등록하면 이벤트가 발생할 때 특정 동작을 수행할 수 있다.

```js
const myInput = document.querySelector('#myInput');
const myBtn = document.querySelector('#myBtn');

myInput.addEventListener('keydown', function () {
    console.log('Keyboard Event');
});

myBtn.addEventListener('click', function() {
    console.log('Mouse Event');
});
```

위 코드에서
- `myInput`에 'keydown' 이벤트가 발생하면 "Keyboard Event"가 출력된다.
- `myBtn`을 클릭하면 "Mouse Event"가 출력된다.

하지만, 이렇게 단순하게만 처리하면 어떤 키가 입력되었는지, 어떤 요소를 클릭했는지 등의 상세 정보를 알기 어렵다.
이런 정보를 얻으려면 **이벤트 객체(Event Object)** 를 활용해야 한다.

## 이벤트 객체란?
웹 페이지에서 이벤트가 발생하면, 그 이벤트와 관련된 다양한 정보를 담고 있는 객체(이벤트 객체) 가 자동으로 생성된다.


이벤트 객체에는 다음과 같은 정보가 포함될 수 있다.
- 이벤트가 발생한 요소 (target)
- 이벤트 타입 (type)

### 이벤트 객체를 활용하는 법

`addEventListener` 메서드의 두 번째 인자로 전달되는 함수(이벤트 핸들러)는 자동으로 이벤트 객체를 첫 번째 인자로 받는다.

```js
function printEvent(event) {
    console.log(event); // 이벤트 객체 출력
}

myInput.addEventListener('keydown', printEvent);
```

위 코드에서
- `printEvent` 함수는 keydown 이벤트가 발생할 때 실행된다.
- 이때 event 객체가 함수의 첫 번째 인자로 자동 전달된다.
- `console.log(event);`를 실행하면, 현재 발생한 이벤트에 대한 다양한 정보가 출력된다.

이 이벤트 객체를 활용하여 DOM 요소 조작 등 다양하게 활용해볼 수 있다.

```js
function eventHandler(event) {
    event.target.style.color = 'red';
}
```

### 함수 파라미터 이름에 따른 차이점
이벤트 핸들러의 첫 번째 매개변수(파라미터)는 자동으로 이벤트 객체를 받는다.
그런데 이 매개변수의 이름을 다르게 지정하거나 아예 생략하면 어떤 차이가 있을까?

```js
function printEvent(event) {
    console.log(event);
}

function printEvent(e) {
    console.log(e);
}

function printEvent() {
    console.log(event);
}
```

각 함수의 차이를 정리하면 다음과 같다.

#### 1. printEvent(event)
```js
function printEvent(event) {
    console.log(event);
}
myInput.addEventListener('keydown', printEvent);
```
- 함수의 첫 번째 매개변수를 event로 지정
- 이벤트 객체는 event라는 이름으로 함수 내부에서 사용 가능
- console.log(event);를 실행하면 KeyboardEvent 객체가 출력됨

#### 2. printEvent(e)
```js
function printEvent(e) {
    console.log(e);
}
myInput.addEventListener('keydown', printEvent);
```
- 매개변수 이름만 `e`로 바뀐 것이므로, `event`를 `e`로 부른 것과 완전히 동일한 동작을 함
- 즉, `event`든 `e`든 이름은 자유롭게 정할 수 있음
- 관례적으로 `event`를 줄여서 `e`로 많이 사용한다.

#### 3. printEvent() (매개변수 없이 사용)
```js
function printEvent() {
    console.log(event);
}
myInput.addEventListener('keydown', printEvent);
```
이렇게 사용하게 되면 다음과 같은 결과가 나타난다.

#### ✅ 전역 컨텍스트에서 event 참조
```js
console.log(event);
```
- 오류 발생 → **Uncaught ReferenceError: event is not defined**
- event 객체는 **이벤트 핸들러 내부에서만 유효**하기 때문에, 전역 스코프에서 직접 참조할 수 없음

#### ✅ `addEventListener` 내부에서 event 참조
```js
myInput.addEventListener('keydown', function () {
    console.log(event);
});
```
- 일부 브라우저(특히 구형 IE) 에서는 `window.event`를 통해 자동으로 이벤트 객체를 참조할 수 있었음
- 현대 브라우저(크롬, 파이어폭스 등) 에서는 `window.event`를 지원하지 않거나 제한적으로 동작
- 크롬에서는 event가 자동으로 바인딩되는 경우가 있지만, 명시적으로 전달하지 않으면 예측 불가능한 동작을 할 수 있음
- 즉, event를 매개변수 없이 직접 참조하는 것은 권장되지 않음

## 이벤트 객체 프로퍼티 종류
이벤트 객체는 이벤트 타입에 따라 서로 다른 프로퍼티를 가지게 된다.

### 1️⃣ 공통 프로퍼티  
모든 이벤트 객체가 **공통적으로** 가지고 있는 프로퍼티

| 프로퍼티 | 설명 |
|---------|------|
| `type` | 이벤트 이름 (`click`, `mouseup`, `keydown` 등) |
| `target` | 이벤트가 발생한 요소 |
| `currentTarget` | 이벤트 핸들러가 등록된 요소 |
| `timeStamp` | 이벤트 발생 시각 (페이지 로드 이후 경과한 밀리초) |
| `bubbles` | 버블링 단계인지 여부 (`true` / `false`) |

### 2️⃣ 마우스 이벤트 프로퍼티  

| 프로퍼티 | 설명 |
|----------|------|
| `button` | 누른 마우스 버튼 (0: 왼쪽, 1: 가운데(휠), 2: 오른쪽) |
| `clientX`, `clientY` | 마우스 커서의 **브라우저 표시 영역**에서의 위치 |
| `pageX`, `pageY` | 마우스 커서의 **문서 영역**에서의 위치 |
| `offsetX`, `offsetY` | 마우스 커서의 **이벤트가 발생한 요소** 내에서의 위치 |
| `screenX`, `screenY` | 마우스 커서의 **모니터 화면** 내에서의 위치 |
| `altKey` | 이벤트 발생 시 `Alt` 키가 눌렸는지 (`true` / `false`) |
| `ctrlKey` | 이벤트 발생 시 `Ctrl` 키가 눌렸는지 (`true` / `false`) |
| `shiftKey` | 이벤트 발생 시 `Shift` 키가 눌렸는지 (`true` / `false`) |
| `metaKey` | 이벤트 발생 시 `Meta` 키가 눌렸는지 (`true` / `false`) <br> (Windows: `Windows` 키, Mac: `Command` 키) |

### 3️⃣ 키보드 이벤트 프로퍼티   

| 프로퍼티 | 설명 |
|----------|------|
| `key` | 눌린 키의 **값** (예: `'a'`, `'Enter'`, `'ArrowUp'`) |
| `code` | 눌린 키의 **물리적 위치** (예: `'KeyA'`, `'Enter'`, `'ArrowUp'`) |
| `altKey` | 이벤트 발생 시 `Alt` 키가 눌렸는지 (`true` / `false`) |
| `ctrlKey` | 이벤트 발생 시 `Ctrl` 키가 눌렸는지 (`true` / `false`) |
| `shiftKey` | 이벤트 발생 시 `Shift` 키가 눌렸는지 (`true` / `false`) |
| `metaKey` | 이벤트 발생 시 `Meta` 키가 눌렸는지 (`true` / `false`) <br> (Windows: `Windows` 키, Mac: `Command` 키) |