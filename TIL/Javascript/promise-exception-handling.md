# 자바스크립트 비동기 프로그래밍 (3)

## 1. Promise 기반의 예외 처리

비동기 작업은 네트워크 장애 등 다양한 이유에서 오류가 발생할 수 있다.
`Promise` 기반 코드에서 예외 처리를 할 때 `try...catch` 문을 사용하면 된다.

```js
async function printData() {
  try {
    const response = await fetch("https://...");
    const data = await response.json();
    console.log(data);
  } catch (e) {
    console.log("error");
  }
}
```

에러 객체를 출력하면 발생한 오류에 대한 정보를 자세하게 확인할 수 있고, 이것을 활용할 수도 있다.

```js
async function printData() {
    ...
    catch (e) {
        console.log(e); // 에러 객체 출력
    }
}
```

`try...catch` 외에 무조건 실행해야 하는 코드는 `finally` 블럭에 작성하면 된다.

```js
async function printData() {
  try {
    const response = await fetch("https://...");
    const data = await response.json();
    console.log(data);
  } catch (e) {
    console.log("error");
  } finally {
    // 에러 여부와 관계 없이 무조건 실행되는 코드
    console.log("Finished");
  }
}
```

### Promise의 상태와 예외 처리 원리

`Promise`는 'Pending', 'Fulfilled', 'Rejected' 3가지 상태가 있다고 했다.

`try`문 안에 있는 `await` 비동기 코드는 `Promise`의 결과 값을 기다리고 있는데, 이때 작업이 실패하여 `Rejected` 상태가 되면 비동기 작업에서 발생한 오류를 결과 값으로 갖게 된다.

이때 `await` 키워드를 사용했으므로 `Promise`에 담긴 결과 값인 오류를 `throw`하는데, 이것을 `catch`문에서 잡아서 예외 처리를 하게 된다.
