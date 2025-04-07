![](https://velog.velcdn.com/images/justhighway/post/f0aa5ada-09b9-4bfb-bd2d-c824d1470adb/image.png)

## 1. 콜백 함수와 클로저

### 1.1. 기본 개념

클로저(Closure)란 **함수가 자신이 생성된 시점의 외부 렉시컬 환경(Lexical Environment)을 계속 참조할 수 있는 특성**을 의미한다.

클로저는

- **일급 객체(First-Class Object)**
- **중첩 함수(Nested Function)**
- **렉시컬 스코프(Lexical Scope)**

를 지원하는 프로그래밍 언어라면 자연스럽게 발생하는 현상이다.

#### 핵심 원리

자바스크립트 함수는 선언(정의)되는 시점의 스코프 정보를 함수 객체 내부의 `[[Environment]]`라는 내부 슬롯에 저장한다. ([ECMA-262 Execution Context](https://tc39.es/ecma262/#sec-execution-contexts) / [Environment Record](https://tc39.es/ecma262/#sec-environment-records))

이후 내부 함수 또는 콜백 함수가 외부 변수를 참조하면 `[[Environment]]` 슬롯을 통해 **함수 생성 당시의 외부 렉시컬 환경에 계속 접근**할 수 있게 된다.

---

### 1.2. 콜백에서 클로저가 발생하는 이유

콜백 함수는 실행 시점이 동기적 또는 비동기적 상황 모두 발생할 수 있다.  
콜백 함수가 클로저를 자연스럽게 발생시키는 대표 패턴은 다음과 같다.

| 패턴               | 대표 예시                   | 실행 특성             |
| ------------------ | --------------------------- | --------------------- |
| 이벤트 기반 처리   | `addEventListener`          | 비동기적 실행         |
| 타이머 기반 처리   | `setTimeout`, `setInterval` | 지연 실행             |
| 고차함수 활용      | `Array.prototype.map` 등    | 순차적 실행           |
| 비동기 데이터 처리 | `fetch` / `Promise`         | 미래 시점 데이터 처리 |
| 상태 유지 목적     | 커스텀 핸들러 반환          | 스코프 캡슐화         |

#### 공통 특성

- 콜백 함수는** 자신이 선언된 스코프가 종료된 이후 실행**될 가능성이 높다.
- 따라서 자바스크립트 엔진은 콜백 함수 내부에서 외부 변수를 참조할 경우  
  **자동으로 `[[Environment]]` 슬롯을 통해 해당 렉시컬 환경을 클로저로 유지**한다.

---

### 1.3. 콜백 클로저 발생 원리 (ECMAScript 사양 & V8 엔진 관점)

콜백 함수가 클로저를 발생시키는 구체적 동작 과정을 정리하면 다음과 같다.

#### 1) 함수 선언 시

- 자바스크립트 엔진은 함수를 생성할 때
  **`[[Environment]]`라는 내부 슬롯을 자동으로 생성**한다.

- 이 슬롯은 함수가 선언된 시점의 **외부 렉시컬 환경(변수 스코프)**을 저장한다.

#### 2) 콜백 등록 시

- 콜백 함수가 전달되거나 등록되더라도  
  `[[Environment]]` 슬롯은 그대로 유지된다.

- 따라서 실행 시점에 상관없이  
  **콜백 함수는 자신이 생성된 환경의 변수에 접근**할 수 있다.

#### 3) 콜백 실행 시

- 콜백 함수 실행 시 자바스크립트 엔진은  
  **`[[Environment]]` 슬롯을 따라 렉시컬 환경 체인을 탐색**한다.

- 이는 **스코프 체인을 타고 외부 변수를 조회하는 과정**이다.

- 클로저로 참조 중인 변수는 GC(Garbage Collection)의 대상이 되지 않으며  
  콜백 함수 실행이 끝나기 전까지 메모리에 남아있게 된다.

---

## 2. 콜백과 클로저 활용 사례

### 2.1. DOM Event Handler 에서 클로저 발생

```js
const fruits = ["apple", "banana", "peach"];

fruits.forEach((fruit) => {
  const $li = document.createElement("li");
  $li.innerText = fruit;

  $li.addEventListener("click", () => {
    alert(`선택한 과일: ${fruit}`);
  });

  document.body.appendChild($li);
});
```

#### 동작 원리

- `forEach` 반복문 내부 컨텍스트마다 `fruit` 변수 존재
- 클릭 이벤트 발생 시 해당 컨텍스트는 종료됐지만
- 콜백 함수가 생성 당시의 `[[Environment]]`를 통해 `fruit`에 접근 가능
- → **클로저 발생**

---

### 2.2. Timer Callback 에서 상태 유지

```js
function createCounter(message) {
  let count = 0;

  const intervalId = setInterval(() => {
    console.log(`${message}: ${++count}`);
    if (count >= 3) clearInterval(intervalId);
  }, 1000);
}

createCounter("현재 카운트");
```

#### 동작 원리

- 타이머 콜백은 비동기적 실행
- 외부 변수 `message`, `count`에 대한 참조 발생
- 콜백 함수가 `[[Environment]]` 슬롯을 통해 해당 변수 스코프를 클로저로 유지
- → 상태 지속적 관리 가능

---

### 2.3. 고차 함수 기반 콜백 클로저 활용

```js
const threshold = 10;

const numbers = [5, 15, 8, 20];

const result = numbers.filter((num) => num > threshold);
```

#### 동작 원리

- `filter` 메서드의 콜백 함수 내부에서 외부 변수 `threshold` 참조
- → 콜백 함수 실행 시 클로저 발생

---

### 2.4. 비동기 데이터 처리 후 클로저로 데이터 유지

```js
fetch("/api/user")
  .then((res) => res.json())
  .then((userData) => {
    document.querySelector("#btn").addEventListener("click", () => {
      alert(`유저 이름: ${userData.name}`);
    });
  });
```

#### 동작 원리

- 서버 응답 데이터 `userData`는 원래 스코프 종료 후 사라져야 함
- 클릭 이벤트 콜백이 `[[Environment]]` 슬롯을 통해 해당 데이터 환경을 참조
- → 사용자의 인터랙션 시점에 안전하게 데이터 접근 가능
