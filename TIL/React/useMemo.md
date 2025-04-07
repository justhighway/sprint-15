![](https://velog.velcdn.com/images/justhighway/post/6d6eea9e-ad10-4f82-82e4-feb3a89149c5/image.jpg)

## 1. Memoization

Memoization은 함수 호출 결과를 **캐싱**해두고,
같은 입력 값으로 다시 호출될 때 **캐시된 값을 재사용하는 최적화 기법**이다.

예를 들어, 어떤 함수 A에 인자 값을 넣고 결과를 얻었다면,  
다음에 같은 인자 값을 전달했을 때 그 값을 다시 계산하지 않고 **기억해뒀던 값을 바로 꺼내 쓰는 방식**이다.

> 메모리(memory) + 저장(ization) = Memoization

주로 재귀 함수처럼 **계산 비용이 큰 함수**에서 Memoization을 적용해 속도를 개선하거나,
같은 입력 값에 대해 매번 계산을 반복하는 낭비를 방지할 때 사용된다.

---

## 2. 자바스크립트 예시 코드

### 2.1. 비효율적인 코드

```js
function add(a, b) {
  return a + b;
}

add(1, 2); // 3
add(1, 2); // 3
```

위 코드는 `add` 함수에 인자를 넘겨 호출할 때마다 같은 계산을 반복 수행한다.
즉, 동일한 결과를 매번 새로 계산함으로써 불필요한 연산 비용이 발생한다.

### 2.2. Memoization 기법 적용

Memoization의 핵심은 **어딘가에 값을 캐싱**하는 것이다.
이렇게 캐싱해둔 값을 통해 같은 연산에 대해 바로 꺼내쓸 수 있게 된다.

```js
function memoizedAdd() {
  const cache = {}; // 클로저를 활용한 캐시 저장소

  return function (a, b) {
    const key = `${a},${b}`; // 인자 조합을 key로 사용
    if (cache[key]) {
      return cache[key]; // 캐시된 값 리턴
    }

    const result = a + b;
    cache[key] = result; // 결과 캐싱
    return result;
  };
}

const add = memoizedAdd();

add(1, 2); // 계산 후 저장 → 3
add(1, 2); // 캐시된 값 리턴 → 3
```

## 3. `useMemo`

React에서는 상태(state)가 변경될 때마다 컴포넌트가 리렌더링 된다.
문제는 컴포넌트 내부에 선언된 함수나 연산 작업들도 컴포넌트가 리렌더링될 때마다 재실행된다는 점이다.

특히 계산 비용이 큰 연산이나 데이터 가공 로직이 리렌더링마다 반복 실행된다면 성능 낭비가 발생할 수 있다.

이를 방지하기 위해 React는 Memoization 기법이 적용된 `useMemo` 훅을 제공한다.
`useMemo`를 사용하면 특정 값이 변경되지 않는 이상, 이전 결과를 메모리에 저장해두고 불필요한 연산 재실행을 막아 성능 최적화를 도울 수 있다.

### 3.1. 사용법

`useMemo`는 특정 연산 결과를 메모이제이션해서 리렌더링 시 불필요한 계산을 막는 React Hook이다.
`useEffect`와 비슷한 문법을 사용한다. 인수로 **콜백함수**와 **의존성 배열**을 전달받는다.

의존성 배열 내부의 값이 변경되었을 때에만 콜백 함수를 다시 실행하고,
변경이 없으면 이전 계산 결과를 그대로 사용하게 된다.

```jsx
useMemo(callback, [deps]);
```

`useEffect`와 다른 점이 있다면 콜백 함수의 계산 결과에 대한 값을 반환한다는 것이다.
따라서 주로 변수에 `useMemo`를 할당해서 쓰는 것이 일반적이다.

```jsx
const memoizedValue = useMemo(() => {
  // 연산 수행 콜백 함수
  return 계산결과;
}, [deps]);
```

### 3.2. 예시

계산 비용이 큰 함수를 최적화할 때 사용할 수 있다.
다음 코드는 배열의 `reduce` 메서드를 실행할 때의 `useMemo` 예시이다.

```jsx
function TotalComponent({ items }) {
  const total = useMemo(() => {
    return items.reduce((acc, cur) => acc + cur, 0);
  }, [items]);

  return <div>총 합계: {total}</div>;
}
```

위 코드에서 의존성 배열에 `items`를 전달해주고 있다. 따라서,

- `items` 값이 바뀌면 콜백 함수를 실행한다.
- `items` 값이 그대로면 캐시된 `total` 값을 재사용한다.

## 4. 주의사항

### 4.1. 필요할 때만 최소한으로 사용할 것

`useMemo`는 계산 비용이 클 때만 사용해야 한다.

데이터 가공, 정렬, 필터링, 재귀, 복잡한 연산 등 연산량이 큰 경우에만 사용을 권장한다.
단순한 사칙연산, 조건문 등은 연산 비용보다 메모리 오버헤드가 더 크기 때문에 사용되지 않는다.

`useMemo` 자체에도 메모리에 저장 비용과 관리 비용이 있다.
무조건 쓰면 좋은 게 아니라, **성능 최적화가 필요한 상황**에서만 사용하는 것이 원칙이다.

### 4.2. 의존성 배열 정확하게 넣기

```jsx
const value = useMemo(() => {
  return func(a, b, c);
}, [a, b, c]); // 사용하는 값 전부 넣기
```

의존성 배열 잘못 넣으면 캐싱 깨지거나 오래된 값 참조하는 버그가 발생한다.
`eslint-plugin-react-hooks` 익스텐션을 사용하면 자동 체크를 할 수 있다.

## 5. `useEffect`와의 차이

언뜻 보면 문법이나 사용처가 `useEffect`와 비슷해보인다.

`useMemo`와 `useEffect`의 핵심 차이점은 **컴포넌트 라이프 사이클 내 언제 실행되는가**에 차이가 있다.

### 5.1. `useEffect`

`useEffect`는 **렌더링이 끝난 다음에 실행되는 훅**이다.

화면에 UI가 그려진 후(commit phase) 실행되며,
비동기 처리 / 데이터 fetch / 구독, 해제 / DOM 조작 등 말 그대로 부수 효과(side effect)를 처리하기 위해 사용한다.

즉, **렌더링 이후에 부수효과를 처리**한다.

### 5.2. `useMemo`

`useMemo`는 **렌더링 과정(render phase) 안에서 동작**한다.

렌더링 과정 중에 필요한 계산 결과를 메모이제이션해서 불필요한 재계산을 방지한다.
즉, **렌더링 과정 중에 계산을 최적화**한다.

두 훅의 다이어그램을 컴포넌트 라이프 사이클 측면에서 그려보면 다음과 같다.

![](https://velog.velcdn.com/images/justhighway/post/7553551b-98b2-43d1-bb01-dcbf88a3e682/image.png)

사실 `useMemo`는 최적화 기법이라는 점에서 `useEffect`보다는 `useCallback`과 비교하는 것이 바람직하다.
