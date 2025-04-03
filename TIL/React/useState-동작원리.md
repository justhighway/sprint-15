# useState의 동작 원리와 상태 업데이트

React는 성능 최적화를 위해 여러 개의 상태 변경을 하나의 렌더링으로 묶어 처리하는 **배치 처리(Batching)**를 수행한다.

따라서 `useState`의 `setter` 함수를 호출해도 즉시 새로운 상태가 반영되지 않는다. 대신 호출된 순간의 상태 값을 캡처하여 기억한 후(스냅샷), 이를 기반으로 새로운 상태를 계산한다.

## useState와 상태 업데이트 방식

React의 `useState`는 **비동기적으로 상태를 업데이트**하며, 한 번의 렌더링이 끝날 때까지 상태를 일괄적으로 배치 처리(Batching)한다.

따라서 `setState`를 호출해도 상태가 즉시 변경되지 않고, **다음 렌더링이 발생해야 새로운 값이 적용**된다.

```jsx
const [count, setCount] = useState(0);

const handleClick = () => {
  setCount(count + 1);
  console.log(count); // 여전히 이전 값(0) 출력됨
};
```

위 코드에서 `setCount(count + 1)`을 실행해도 `console.log(count)`는 기존 값(0)을 출력한다.

## 배치 처리(Batching)

React는 여러 개의 상태 변경을 한 번의 렌더링으로 묶어 처리하는 최적화 기법을 제공한다. 이를 **배치 처리(Batch Processing)**라고 한다.

```jsx
const handleClick = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
};
```

위 코드가 실행되면 `count` 값이 3 증가할 것 같지만, 실제로는 1만 증가한다.

React는 여러 개의 `setState` 호출을 감지하고, 한 번의 렌더링에서 최종적인 상태만 적용한다.

즉, `count`의 초기 값(0)을 기반으로 `setCount(count + 1)`이 여러 번 호출되지만, 모든 호출이 같은 값을 참고하기 때문에 최종적으로 `count + 1`만 적용된다.

## 함수형 업데이트 (Functional Update)

배치 처리의 영향을 방지하려면 **함수형 업데이트(Function Update)**를 사용해야 한다.

`useState`의 `setter` 함수는 콜백 함수를 인자로 받을 수 있으며, 이 콜백의 매개변수는 가장 최신 상태 값을 보장한다.

이를 활용하면 배치 처리의 영향을 받지 않고 상태를 안전하게 업데이트할 수 있다.

```jsx
const handleClick = () => {
  setCount((prevCount) => prevCount + 1);
  setCount((prevCount) => prevCount + 1);
  setCount((prevCount) => prevCount + 1);
};
```

이제 `setCount`가 실행될 때마다 최신 상태를 참조하므로 `count`가 정상적으로 +3 증가한다.

## 비동기 함수와 상태 업데이트

`setState`가 **비동기적으로 동작**하기 때문에, `fetch` 같은 비동기 함수 내부에서 상태를 변경할 때 주의해야 한다.

```jsx
const fetchData = async () => {
  const response = await fetch("https://api.example.com/data");
  const data = await response.json();

  setCount(count + data.increment); // 이전 count 값을 기준으로 상태 업데이트
};
```

위 코드를 실행하면 `setCount`가 호출 될 때 `count` 값이 **비동기 요청이 시작된 시점의 값**을 기반으로 계산된다.

해결 방법은 **함수형 업데이트**를 사용하는 것이다.

```jsx
const fetchData = async () => {
  const response = await fetch("https://api.example.com/data");
  const data = await response.json();

  setCount((prevCount) => prevCount + data.increment); // 최신 상태 값을 기반으로 업데이트
};
```

이제 `prevCount`가 최신 상태 값을 참조하기 때문에, `count`가 정상적으로 증가한다.

## 정리

1. **React의 `useState`는 상태 업데이트 방식과 렌더링 메커니즘의 영향을 받는다.**
2. **여러 개의 `setState` 호출은 배치 처리되어 최적화**되므로, 예기치 않은 동작을 방지하려면 **함수형 업데이트를 사용하는 것이 안전**하다.
3. **비동기 함수 내부에서 상태를 업데이트할 때도 함수형 업데이트를 활용**하면 최신 상태 값을 안정적으로 참조할 수 있다.
