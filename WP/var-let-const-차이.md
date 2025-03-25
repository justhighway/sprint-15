# var, let, const 를 중복 선언 허용, 스코프, 호이스팅 관점에서 서로 비교해 주세요.

## 중복 선언 허용

|                | var | let, const |
| -------------- | --- | ---------- |
| 중복 선언 허용 | O   | X          |

한 스코프에서 같은 변수를 `let` 또는 `const`로 두 번 선언하면 에러가 발생합니다.

```js
let user;
let user; // SyntaxError: 'user' has already been declared
```

`var`는 변수의 중복 선언을 허용합니다. `var`로 같은 변수를 여러 번 중복으로 선언할 수 있습니다. 하지만 이미 선언된 변수에 `var`를 사용하면 두 번째 선언문은 무시됩니다.

```js
var user = "Pete";
var user = "John"; // 이 "var"는 아무것도 하지 않습니다(이전에 이미 선언됨).
// ...에러 또한 발생하지 않습니다.

alert(user); // John
```

## 스코프

|        | var         | let, const  |
| ------ | ----------- | ----------- |
| 스코프 | 함수 스코프 | 블록 스코프 |

함수를 기준으로만 적용되는 스코프를 함수 스코프, 코드 블록(`{}` 중괄호로 감싸진 부분)을 기준으로 적용되는 스코프를 블록 스코프라고 합니다.

`var`로 선언한 변수의 스코프는 함수 스코프이거나 전역 스코프입니다. 블록 기준으로 스코프가 생기지 않기 때문에 블록 밖에서 접근 가능합니다.

```js
if (true) {
  var test = true;
}

alert(test); // true
```

`var`는 **코드 블록을 무시**하기 때문에 `test`는 **전역 변수**가 됩니다. 전역 스코프에서 이 변수에 접근할 수 있죠.

두 번째 행에서 `var test`가 아닌 `let test`를 사용했다면, 변수 `test`는 `if`문 안에서만 접근할 수 있습니다.

```js
if (true) {
  let test = true;
}

alert(test); // Error: test is not defined
```

코드 블록이 function 안에 있다면, `var`는 함수 레벨 변수가 됩니다.

```js
function sayHi() {
  if (true) {
    var phrase = "Hello";
  }

  alert(phrase); // 제대로 출력됩니다.
}

sayHi();
alert(phrase); // Error: phrase is not defined
```

## 호이스팅

`let`과 `const`로 선언한 변수는 선언되기 이전에 사용될 수 없습니다.

```js
console.log(myVariable);
let myVariable; // Uncaught ReferenceError: Cannot access 'myVariable' before initialization
```

하지만, `var` 변수는 함수 스코프를 기준으로 선언되기 이전에도 변수에 접근이 가능합니다.

```js
console.log(myVariable);
var myVariable; //undefined
```

var 선언은 함수가 시작될 때 처리됩니다. 전역에서 선언한 변수라면 스크립트가 시작될 때 처리되죠.

함수 본문 내에서 `var`로 선언한 변수는 선언 위치와 상관없이 함수 본문이 시작되는 지점에서 정의됩니다.

따라서 아래 두 예제는 동일하게 동작합니다. `var phrase`가 위로 이동한 것처럼 말이죠.

```js
function sayHi() {
  phrase = "Hello";

  alert(phrase);

  var phrase;
}
sayHi();
```

```js
function sayHi() {
  var phrase;

  phrase = "Hello";

  alert(phrase);
}
sayHi();
```
