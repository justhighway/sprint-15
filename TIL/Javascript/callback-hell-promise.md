# 자바스크립트 비동기 프로그래밍 (1)

웹 프로그래밍을 하다 보면, 웹 요청과 같이 실행하는 데 시간이 오래 걸리는 코드가 있다.
이런 작업을 효율적으로 처리하기 위해 **비동기 프로그래밍**이 사용된다.

비동기 프로그래밍의 핵심 개념은 **오래 걸리는 작업이 있을 경우, 그 작업을 기다리지 않고 다음 작업을 먼저 처리한 후 다시 돌아와서 실행하는 것**이다.

## 1. Callback (콜백 함수)

콜백 함수는 **다른 함수의 인자로 전달되는 함수**를 의미한다.

보통 함수 선언문을 이용해 전달하지만, 간단한 경우 화살표 함수도 활용한다.

```js
function sayHello(name) {
  console.log(`Hello, ${name}!`);
}

function print(func, name) {
  func(name);
}

// 아래 두 코드는 같은 결과를 출력한다.
print(sayHello, "Alice");
print((name) => console.log(`Hello, ${name}!`), "Alice");
```

## 2. 비동기 실행의 특징

### 1) 비동기 함수는 이후 코드들을 먼저 실행한 후 콜백을 실행한다.

아래 코드를 보면 `setTimeout`의 delay 값을 `0`으로 설정해도, 해당 콜백은 마지막에 실행된다.
**비동기 함수의 콜백은 비동기 함수 이후에 있는 코드가 모두 실행된 후 실행된다**는 특징 때문이다.

```js
console.log("1");
setTimeout(() => console.log("2"), 0);
console.log("3");
console.log("4");
console.log("5");
console.log("6");
console.log("7");
console.log("8");
console.log("9");
console.log("10");
```

#### 실행 결과:

```js
1;
3;
4;
5;
6;
7;
8;
9;
10;
2; // 비동기 함수 콜백이 마지막에 실행됨
```

> 비동기 함수 이후의 코드가 오래 걸리더라도, 모든 코드 실행이 끝난 후 콜백이 실행된다.

### 2) 여러 개의 콜백이 있으면 순차적으로 실행된다.

비동기 작업이 여러 개 존재할 경우, 콜백들은 **동기적으로 실행**된다.
두 개의 `setTimeout` 함수가 있고, 두 번째 함수의 딜레이 시간이 더 짧다면 두 번째 함수가 먼저 실행된다.

```js
console.log("1");

setTimeout(() => console.log("2"), 1001);
setTimeout(() => console.log("3"), 1000);

console.log("4");
```

#### 실행 결과:

```js
1;
4;
3;
2;
```

## 3. 콜백 지옥 (Callback Hell)

비동기 작업이 연속적으로 실행될 경우, 콜백을 계속 중첩해서 사용해야 한다. 이를 **콜백 지옥**(callback hell)이라고 한다.

```js
getEmployees((response) => {
  json(response, (data) => {
    groupEmployees(data, (result) => {
      console.log(result);
    });
  });
});
```

## 4. Promise

콜백 지옥은 코드 가독성을 크게 저하시킨다. 이를 해결하기 위해 등장한 것이 **Promise**다.

**Promise**는 ES6에서 도입된 개념으로, 비동기 작업의 결과를 나타내는 객체다. Promise는 비동기 작업이 완료되면 결과 값을 알려줄 것을 "약속"한다.

### 1) 콜백 헬 vs Promise

Promise를 활용하면 코드가 훨씬 간결해진다.

#### 콜백 헬 방식

```js
getEmployees((response) => {
  json(response, (data) => {
    console.log(data);
  });
});
```

#### Promise 방식

```js
const response = await fetch("...");
const data = await response.json();
console.log(data);
```

### 2) Promise의 상태

- **Pending**: 비동기 작업이 진행 중
- **Fulfilled**: 비동기 작업이 성공적으로 완료됨
- **Rejected**: 비동기 작업이 실패함
