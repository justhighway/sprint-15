# useEffect

`useEffect`는 부수효과(side effect)를 실행시켜주는 리액트 훅이다.

컴포넌트가 화면에 마운트 될 때 처음 실행되고, 언마운트 될 때 클린업 함수가 실행된다.

## 처음 한 번만 실행하기

```jsx
useEffect(() => {
  // 실행할 코드
}, []);
```

컴포넌트가 처음 마운트되고 나면 리액트가 콜백 함수를 기억해뒀다가 실행한다.

그 이후로는 콜백 함수를 실행하지 않는다.

콜백 함수가 실행되면 컴포넌트가 재렌더링 된다.

## 값이 바뀔 때마다 실행하기

```jsx
useEffect(() => {
    // 실행할 코드
}, [dep1, dep2, dep3, ...])
```

`useEffect`의 두 번째 인자로 전달되는 배열은 **의존성 배열**(Dependency List)이다.

이 의존성 배열에 전달한 상태 값들 중 하나라도 바뀌면 콜백 함수를 실행한다.

콜백 함수가 실행되면 컴포넌트가 재렌더링 된다.

## 예시 코드

```jsx
import { useEffect, useState } from "react";

function App() {
  const [first, setFirst] = useState(1);
  const [second, setSecond] = useState(1);

  const handleFirstClick = () => setFirst(first + 1);

  const handleSecondClick = () => setSecond(second + 1);

  useEffect(() => {
    console.log("렌더링 이후", first, second);
  }, []);

  console.log("렌더링", first, second);

  return (
    <div>
      <h1>
        {first}, {second}
      </h1>
      <button onClick={handleFirstClick}>First</button>
      <button onClick={handleSecondClick}>Second</button>
    </div>
  );
}

export default App;
```
