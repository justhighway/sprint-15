## 1.리액트 컴포넌트의 라이프 사이클

리액트는 `Mount`, `Update`, `Unmount` 3가지 단계로 구분되는 라이프사이클을 갖는다.

### 1) Mount

- 컴포넌트가 탄생하는 순간
- 화면에 처음 렌더링 되는 순간
- "A 컴포넌트가 Mount 되었다" => A 컴포넌트가 화면에 처음으로 렌더링 되었다.
- ex) 서버에서 데이터 불러오기

### 2) Update

- 마운트 이후 컴포넌트가 다시 렌더링 되는 순간
- 리렌더링 될 때를 의미
- "A 컴포넌트가 Update 되었다" => A 컴포넌트가 리렌더링 되었다.
- ex) 어떤 값이 변경되었는지 콘솔에 출력

### 3) Unmount

- 컴포넌트가 화면에서 사라지는 순간
- 렌더링에서 제외되는 순간을 의미
- "A 컴포넌트가 Unmount 되었다" => A 컴포넌트가 화면에서 사라졌다.
- ex) 컴포넌트가 사용하면 메모리 정리 (클린업)

## 2. useEffect로 라이프 사이클 제어하기

컴포넌트 라이프 사이클의 단계별로 컴포넌트들이 각각 다른 작업을 수행할 수 있도록 하는 것을 "**라이프 사이클 제어**" 라고 한다.

라이프 사이클 제어는 `useEffect`를 사용해서 구현할 수 있다.

### 1) Mount

컴포넌트 함수 내에서 `useEffect`의 의존성 배열(deps)로 빈 배열(`[]`)을 넘겨주면 컴포넌트가 마운트 될 때 딱 한 번만 실행된다.

컴포넌트 내부에서 state 값이 변경되어 컴포넌트가 리렌더링 되어도 콜백 함수가 실행되지 않는다.

```jsx
useEffect(() => {
  console.log("Mount!");
}, []);
```

### 2) Update

`useEffect`에 의존성 배열을 전달하지 않으면, 컴포넌트가 리렌더링 될 때에만 콜백 함수를 실행시킬 수 있다.

```jsx
useEffect(() => {
  console.log("Updated");
});
```

> **⚠️ 주의!**
>
> 의존성 배열을 전달하지 않는 `useEffect` 내부에 `setState`를 전달하면 state가 변경될 때마다 `useEffect`가 실행되므로 **무한 리렌더링**이 발생한다. 따라서 `useEffect` 내부에서 상태 값을 변경하는 경우, 의존성 배열을 무조건 전달해야 한다.
>
> ```jsx
> useEffect(() => {
>   // 무한 리렌더링 발생!
>   setCount(count + 1);
> });
> ```

의존성 배열 내에 state를 전달하면, 해당 state가 변경될 때마다 useEffect 함수가 실행된다.

(컴포넌트가 마운트될 때에도 당연히 실행된다.)

```jsx
useEffect(() => {
  console.log("State Updated!");
}, [state]); // state가 변경될 때마다 콜백 함수 재실행
```

다음과 같이 **마운트 여부로 `useRef`를 flag 역할로 사용**해서 컴포넌트가 리렌더링 될 때에만 콜백 함수를 실행시켜줄 수도 있다.

```jsx
// ref 객체는 리렌더링에 영향을 주지 않는다.
const isMount = useRef(false);

useEffect(() => {
  if (!isMount.current) {
    isMount.current = true;
    return;
  }
  console.log("State Updated!");
}, [state]);
```

### 3) Unmount

`useEffect` 콜백 함수 내부에 함수를 리턴할 수 있는데, 이 함수는 클린업 함수(clean-up)이다.

클린 업은 컴포넌트가 언마운트될 때 실행되는 콜백 함수로, 이벤트 리스너나 타이머 정리 등 메모리 정리에 사용된다.

```jsx
useEffect(() => {
  // 클린업, 정리 함수
  return () => {
    console.log("Unmount");
  };
}, []);
```
