# 자바스크립트 비동기 프로그래밍 (4)

## 1. then()

- Promise 객체의 메서드
- 앞선 비동기 작업이 완료되면 등록된 콜백 함수를 실행
- `then` 메서드를 연속으로 연결하는 방식을 **Promise 체이닝 (Promise Chaining)**이라고 함
- 현재 비동기 작업이 완료되어 `Promise`가 fulfilled 상태가 되면, 다음 `then`에서 해당 결과 값을 받아 콜백을 수행

```js
fetch("https://...")
  .then((response) => response.json())
  .then((data) => console.log(data));
```

- 보통 코드가 무언가를 기다릴 때(코드가 Promise를 활용할 때) `.then()`으로 따로 감싸 주고, 그렇지 않다면 그냥 콜백의 본문에 코드를 이어서 쓰면 된다.

```js
function getRandomElement(arr) {
  const randomIdx = Math.floor(Math.random() * arr.length);
  return arr[randomIdx];
}

console.log("메뉴 고르는 중...");

fetch("https://learn.codeit.kr/api/menus")
  .then((response) => response.json())
  .then((menus) => {
    // 동기 코드에 대해서는 콜백 본문에 코드 이어서 작성
    const randomMenu = getRandomElement(menus);
    console.log(`오늘의 랜덤 메뉴는 ${randomMenu.name}입니다!`);
  });
```

## 2. catch(), finally()

- Promise 체인에서 발생하는 오류는 `.catch()` 메소드로 처리할 수 있음
- Promise 체인에서 항상 실행해야 하는 코드는 `.finally()`로 처리할 수 있음

- `.then()`은 앞선 Promise가 **fulfilled** 상태가 되면 콜백을 실행
- `.catch()`는 앞선 Promise가 **rejected** 상태가 되면 콜백을 실행
- `.finally()`는 앞선 Promise가 **fulfilled** 또는 **rejected** 상태가 되면 콜백을 실행

### catch()

- 앞선 `Promise`가 **Rejected** 상태일 때 실행 (Fulfilled 상태일 때는 실행되지 않음)
- `catch`에서 Rejected Promise 결과 값에 대한 콜백을 잘 실행하면 `catch`가 리턴하는 `Promise`는 Rejected가 아닌 Fulfilled 상태가 됨
- 프로미스 체인 어디서 오류가 발생하든, Rejected 상태의 프로미스가 계속 전파되는 원리 때문에 최종적으로 `.catch()`에서 오류를 처리할 수 있음
- 따라서 보통 `.then()` 뒤에 `.catch()` 연결

### finally()

- 앞선 `Promise`의 상태가 Fulfilled 또는 Rejected일 때 등록된 콜백 실행
- 프로미스 체인 결과와 상관 없이 무조건 실행되어야 하는 코드를 `finally`에 등록

```js
fetch("https://...")
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.log("error"))
  .finally(() => console.log("finished"));
```

## 3. Promise.all()

- 여러 개의 Promise를 병렬로 실행하고, 모든 Promise가 완료될 때까지 기다리는 메서드
- **모든 Promise가 성공(Fulfilled)**하면 각 Promise의 결과 값들을 담은 배열을 반환
- **하나라도 실패(Rejected)**하면 즉시 Rejected 상태로 전환되며, 해당 에러를 반환

```js
const promise1 = fetch("https://...").then((res) => res.json());
const promise2 = fetch("https://...").then((res) => res.json());

Promise.all([promise1, promise2])
  .then((results) => {
    console.log("모든 요청 완료:", results); // 모든 응답이 성공하면 배열로 결과값 반환
  })
  .catch((error) => {
    console.log("하나 이상의 요청 실패:", error); // 하나라도 실패하면 즉시 실행
  });
```

- `Promise.all()`은 배열로 넘겨진 모든 Promise들의 성공 결과 값을 하나로 묶어 반환함
- `Promise`를 반환하며, 배열의 각 Promise가 성공적으로 완료되면 그 결과 값을 담은 배열을 반환
- 하나라도 실패하면 즉시 `catch()`로 이동하여 에러를 처리

### 좋은 예제

```js
// asyncFunctions.js
export async function getEmployees() {
  try {
    const response = await fetch("https://learn.codeit.kr/api/employees");
    const employees = await response.json();
    return employees;
  } catch (error) {
    console.log("데이터를 가져오지 못했습니다 :(");
    return null;
  }
}

export async function getMenus() {
  try {
    const response = await fetch("https://learn.codeit.kr/api/menus");
    const menus = await response.json();
    return menus;
  } catch (error) {
    console.log("데이터를 가져오지 못했습니다 :(");
    return null;
  }
}

// main.js
import { getEmployees, getMenus } from "./asyncFunctions.js";

// 여기에 코드를 작성하세요.
const employeesPromise = getEmployees();
const menusPromise = getMenus();

const [employees, menus] = await Promise.all([employeesPromise, menusPromise]);

// 테스트 코드
console.log("직원 데이터:");
console.log(employees);
console.log("메뉴 데이터:");
console.log(menus);
```
