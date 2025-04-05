# JS 요리교실 4주차 (콜백 함수)

## 4-2-2 인자

- 콜백 함수도 일반적인 함수와 동일하게 외부에서 인자(arguments)를 전달받을 수 있다.
- 예: `map` 메서드는 각 배열 요소에 대해 콜백을 호출할 때, 특정 인자들을 전달한다.

#### 예시

```js
const arr = [10, 20, 30];

const newArr = arr.map((currentValue, index) => {
  console.log(currentValue, index);
  return currentValue * 2; // 반환값이 없으면 `undefined`가 저장됨
});

console.log(newArr);
```

#### 실행 결과

```zsh
10 0
20 1
30 2
[20, 40, 60]
```

> 콜백 함수는 반드시 새로운 값을 반환(return)해야 하며, 그렇지 않으면 **undefined가 저장**된다.

### 1) 콜백 함수의 인자에 대한 제어권

- 콜백 함수의 인자는 **함수 호출 주체에 의해 결정**된다
- 즉, **콜백을 호출하는 주체(`map` 메서드)가 인자의 순서와 개수를 결정**한다.

#### `map` 메서드의 문법 (= 정의된 규칙)

- `map` 메서드를 호출하는 사용자는 **콜백 함수의 구조를 변경할 수 없다**.
- 콜백을 호출하는 주체는 `map` 메서드이며, 전달하는 인자의 순서 또한 `map` 메서드가 정한다.

```js
array.map(callback(currentValue, [currentIndex, array, ...args]));
```

이 점을 이해 못하면 다음과 같은 실수를 할 수 있다.

```js
const arr = [10, 20, 30];

// 인자의 이름을 임의로 변경한 경우
const newArr = arr.map((index, currentValue) => {
  console.log(index, currentValue);
  return currentValue * 2;
});

console.log(newArr);
```

#### 실행 결과

```zsh
10 0
20 1
30 2
[NaN, NaN, NaN]
```

- `map` 메서드는 첫 번째 인자로 `currentValue`, 두 번째 인자로 `index`를 전달한다. (규칙)
- 사용자가 변수명을 `index`, `currentValue`로 변경하면서 잘못된 값이 매핑되었다.
- `index`에는 `currentValue`가, `currentValue`에는 `index`가 들어가면서 연산이 엉망이 되어 `NaN`이 출력된다.

### 2) 콜백 호출 주체가 가지는 인자에 대한 제어권

**콜백을 호출하는 주체**가 어떤 인자를 전달하는지, 몇 개를 전달하는지를 **결정**한다는 개념을 더 명확하게 이해하기 위해 다음 예제를 살펴보자.

```js
function foo(callback) {
  callback(10); // 콜백을 호출하며 인자로 10을 전달
}

function bar(a, b, c, d, e, f, g) {
  console.log(a, b, c, d, e, f, g);
}

foo(bar);
```

#### 출력 결과

```zsh
10 undefined undefined undefined undefined undefined undefined
```

- `foo(bar)`에서 `bar` 함수는 `foo` 내부에서 `callback(10)` 형태로 호출되었다.
- 이때, `bar`의 첫 번째 인자 `a`는 `10`을 받는다.
- 하지만 `bar`는 총 7개의 인자를 받을 수 있도록 선언되어 있다.
- 나머지 인자는 `undefined`가 된다.

> 즉, **콜백을 호출하는 주체(`foo` 함수)가 인자의 개수와 값을 결정**한다.
> 사용자는 `bar` 함수의 파라미터 개수를 늘려도, **호출 주체가 제공하는 인자보다 많은 값**은 `undefined`로 채워진다.

## 4-2-3 this

- 콜백 함수도 일반적인 함수이기 때문에 기본적으로 `this`는 **전역 객체를 참조**한다.
- 콜백을 호출하는 주체가 **명시적으로 `this`를 지정하면, 해당 객체를 참조**한다.

### 1) 콜백 함수 내부에서의 this

```js
/* 기본적인 콜백 함수의 this */
setTimeout(() => console.log(this), 1000); // Window { ... }

/* 명시적 바인딩을 활용한 콜백 함수의 this */
/* 콜백 호출 주체: addEventListener */
const button = document.querySelector(".button");
button.addEventListener("click", function () {
  console.log(this); // <button class='button'>클릭</button>
});
```
