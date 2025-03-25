# 자바스크립트 this에 대해 설명해 주세요.

## this 란?

자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수를 말합니다.

자바스크립트에서 this 는 호출방식에 따라서 동적으로 결정됩니다.

## 바인딩이란?

함수 또는 메소드를 호출한 대상에 실제 함수를 연결해주는 것을 말합니다.

자바스크립트에서 this 는 어떤 환경에서 사용하는지에 따라 this 에 바인딩되는 객체가 달라집니다.

## 전역 환경

웹 브라우저, 전역 환경에서 `this` 는 전역 객체인 `window` 가 바인딩됩니다.

```js
console.log(this === window); // true
```

## 함수 내부

함수 내부에서 `this` 의 값은 함수를 호출한 방법에 의해 결정됩니다.

### 단순 호출

전역에서 함수를 호출하고 있어 `this` 는 전역 객체인 `window` 가 바인딩됩니다.

```js
function returnThis() {
  return this;
}

console.log(returnThis() === window); // true
```

### 객체의 메소드 호출

함수를 어떤 객체의 메소드로 호출하면 `this` 의 값은 그 객체가 바인딩됩니다.

```js
function returnThis() {.
    return this;
}

const obj = {
    name: 'obj',
    returnThis: returnThis
};

console.log(obj.returnThis() === obj); // true
```

### Arrow Function

일반 함수처럼 호출한 대상에 따라 상대적으로 변하는 것이 아니라, Arrow Function을 감싸는 정적 범위(Lexical Scope)인 선언되기 직전에 유효한 `this` 를 갖게 됩니다.

아래에 `returnThis` 가 선언되기 직전에 유효한 `this` 는 `window` 객체입니다.

```js
const returnThis = () => this;

const obj = {
  name: "obj",
  returnThis: returnThis,
  returnThis2: () => this,
};

console.log(obj.returnThis() === window); // true
```

### DOM 이벤트 처리

이벤트 처리에 사용한 함수에서 `this` 는 이벤트를 보낸 요소로 설정됩니다.

```js
function checkThis(event) {
  console.log(this === event.currentTarget); // true
}

const element = document.getElementById("something");

element.addEventListener("click", checkThis, false);
```

### 생성자 함수 호출

함수를 `new` 키워드와 함께 생성자로 사용하면 `this` 는 새로 생긴 인스턴스가 됩니다.

```js
function Person(name) {
  this.name = name;
}

// new 연산자로 호출한 경우
const obj = new Person("이름");
console.log(obj.name === "이름"); // true

// new 연산자 없이 호출한 경우
const obj2 = Person("윈도우");
console.log(obj2 === undefined); // true
console.log(window.name === "윈도우"); // true
```

### bind 메소드

위에서 살펴본 것들은 `this` 로 설정될 객체가 함수 호출 패턴에 의해 결정된 것들입니다. 이렇게 암묵적인 방법 이외에 `this` 를 특정 객체에 명시적으로 바인딩하는 방법도 있습니다. `obj2` 에서 실행하는 `returnThis` 에 `this` 로 `obj` 가 바인딩 된 예시입니다.

```js
function returnThis() {
  return this;
}

const obj = { name: "obj" };
const obj2 = returnThis.bind(obj)();

console.log(obj === obj2); // true
```
