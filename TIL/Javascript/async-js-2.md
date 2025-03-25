# 자바스크립트 비동기 프로그래밍 (2)

## 1. await

`Promise`는 처음에는 **Pending 상태**다. 아래 코드는 비동기 작업이 끝나지 않았는데 바로 `response`에 할당하므로 `Promise<Pending>`이 된다.

```js
const response = fetch("https://..."); // fetch 함수는 Promise 반환
console.log(response); // Promise<Pending>
```

`await` 키워드를 사용하면, **Promise가 Fulfilled 또는 Rejected 상태가 될 때까지 기다렸다가 결과를 반환한다.**

```js
const response = await fetch("https://...");
console.log(response); // response: {...}
```

`response` 데이터를 활용하려면 `json()`을 이용해 파싱해야 한다.
`json()` 역시 `Promise`를 반환하는 비동기 함수이므로 `await` 키워드를 사용해야 한다.

```js
const response = await fetch("https://...");
const data = await response.json();
console.log(data); // [ {...} ]
```

### 핵심 정리

- `Promise`를 반환하는 표현식 앞에 `await`을 붙이면 결과 값을 직접 받아올 수 있다.
- `await`은 `Promise`가 완료될 때까지 기다린 후 결과 값을 반환한다.

## 2. async

비동기 작업의 목적은 **비동기 작업을 기다리는 동안 다른 작업을 먼저 처리하는 것**이다. 하지만 `await`을 사용하면 코드가 순차적으로 실행되는 것처럼 보일 수 있다.

```js
const response = await fetch("https://...");
const data = await response.json();
console.log(data);
console.log("Task 2");
console.log("Task 3");
```

#### 실행 결과:

`data`를 출력한 후 `Task 2`, `Task 3`이 차례로 실행된다. 즉, 오래 걸리는 비동기 작업을 다 수행한 후에야 다음 코드들이 실행된다.

```js
[
  {
    data...
  }
]
Task 2
Task 3
```

**비동기 처리를 제대로 하기 위해서는 `async` 함수를 활용해야 한다.** `await` 키워드는 `async` 함수 내부에서만 사용할 수 있다.

```js
const printEmployees = async () => {
  const response = await fetch("https://...");
  const data = await response.json();
  console.log(data);
};

printEmployees();
console.log("Task 2");
console.log("Task 3");
```

#### 실행 결과:

`Task 2`, `Task 3`이 먼저 실행된 후 `data`가 출력된다.

```js
Task 2
Task 3
[
  {
    ...data
  }
]
```

`async` 함수가 실행될 때 내부의 `await`이 코드 실행을 블로킹하지 않기 때문이다. 즉, `await`이 있는 부분에서 `Promise`가 완료될 때까지 기다리는 동안, 함수 바깥의 코드가 먼저 실행된다.

### 정리

- `async` 함수 내부에서 `await`을 사용하여 비동기 작업을 처리할 수 있다.
- `async` 함수는 비동기 작업이 완료될 때까지 기다리지 않고, 다음 코드 실행을 계속 진행한다.

## 3. 효율적인 async / await 코드

`async` 함수 내부에서 `await`을 사용하여 반복적으로 비동기 작업을 수행하면 실행 속도가 느려질 수 있다.

```js
async function print() {
  for (let i = 1; i <= 10; i++) {
    const response = await fetch(`http://...${i}`);
    const data = await response.json();
    console.log(data);
  }
}
```

위 코드는 다음과 같은 방식으로 실행된다.

```js
async function print() {
  const response = await fetch("http://...1");
  const data = await response.json();
  console.log(data);

  const response = await fetch("http://...2");
  const data = await response.json();
  console.log(data);

  const response = await fetch("http://...3");
  const data = await response.json();
  console.log(data);
  ...
}
```

각 요청이 완료될 때까지 기다려야 하므로 실행 속도가 느려진다.

좀 더 효율적으로 코드를 작성하려면 다음과 같이 작성하면 된다.

```js
async function print(id) {
  const response = await fetch(`http://...${id}`);
  const data = await reponse.json();
  console.log(data);
}

for (let i = 1; i < 11; i++) {
  print(i);
}
```

위 코드가 실행되는 과정을 풀어 쓰면 다음과 같다.

```js
async function print(id) {
  const response = await fetch(`http://...${id}`);
  const data = await reponse.json();
  console.log(data);
}

print(1);
print(2);
print(3);
```

`async` 함수 외부에 반복문을 적용해서 `print` 함수를 호출하는 방식으로 수정했다. `async` 함수에서 `await`을 마주치면, 해당 `Promise`가 fulfilled가 될 때까지 함수 바깥으로 빠져나가 다음 코드를 진행한다. 이런 원리로 1번부터 10번까지 데이터에 대한 리퀘스트를 거의 동시에 할 수 있게 된다.

즉, `async`와 `await`을 사용하여 비동기 코드를 작성할 땐 효율적으로 작성하는 방법을 고민해야 한다.

## 4. async 함수의 반환 값

`async` 함수는 무조건 `Promise`를 반환한다.

```js
async function print() {
  const response = await fetch("http://...");
  const data = await response.json();
  console.log(data);
}

const result = print();
console.log(result); // Promise<Pending>
```

비동기 함수의 반환 값을 사용하려면 `await`을 붙여야 한다.

```js
async function print() {
  const response = await fetch("http://...");
  const data = await response.json();
  return data;
}

const result = await print();
console.log(result); // [ {...data} ]
```
