# `this`와 thisBinding

객체는 상태<sup>state</sup>를 나타내는 **프로퍼티**들과 동작<sup>behavior</sup>을 나타내는 **메서드**들을 하나의 논리 단위로 묶은 복합 자료구조다.

메서드는 자신이 속한 객체의 프로퍼티를 참조하고 변경할 수 있어야 하는데, 이때 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다. 즉, 메서드는 자기 자신이 속한 객체가 무엇인지 참조할 수 있어야 한다.

## 메서드의 자기 참조

### 재귀적 참조

객체 리터럴 방식으로 생성한 객체의 경우, 메서드 내부에서 메서드 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조할 수 있다.

```js
1  const circle = {
2      radius: 5,
3      getDiameter() {
4          return 2 * circle.radius;
5      }
6  };
7
8  console.log(circle.getDiameter()); // 10
```

위 코드의 `getDiameter` 메서드 내에서, 메서드 자신이 속한 객체를 가리키는 식별자 `circle`을 참조하고 있다.

이 참조 표현식(`circle.radius`)이 평가되는 시점은 `getDiameter` 메서드가 호출되어 함수 내부가 실행되는 시점이다.

자바스크립트에서 변수와 함수는 값이 할당되기 직전에 평가(검사)되므로, `getDiameter` 메서드가 호출되는 시점(8번 줄)에는 객체 리터럴의 평가가 완료되어 객체가 생성되었고, `circle` 식별자에 생성된 객체가 할당된 이후이다. 따라서 메서드 내부에서 `circle` 식별자를 참조할 수 있다. (호이스팅과 관련된 개념)

### 재귀적 참조의 문제

자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 무한 루프나 예상치 못한 동작을 일으킬 가능성이 높기 때문에 바람직하지 않다.

```js
const obj = {
  value: 10,
  self: obj, // obj가 자기 자신을 참조
};
```

위와 같은 구조에서 `obj.self`는 자신의 객체 `obj`를 참조하고, `obj.self.self`는 또 다시 `obj`를 참조하는 식이 되어 **무한 참조**가 발생할 수 있다. 이런 방식은 코드가 예기치 않게 동작할 수 있기 때문에 피하는 것이 좋다.

### 생성자 함수 인스턴스에서의 자기 참조

생성자 함수 방식으로 인스턴스를 생성하는 경우를 생각해보자.

```js
// 생성자 함수 Circle
function Circle(radius) {
    // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
    ???.radius = radius;
}

// prototype을 활용하여 메서드 등록
Circle.prototype.getDiameter = function () {
    // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
    return 2 * ???.radius;
};

// new 생성자를 활용한 `circle` 인스턴스 생성
const circle = new Circle(5);
```

위 코드의 흐름을 보면,

1. `Circle`이라는 생성자 함수를 정의한다. `radius` 파라미터를 받고, 생성할 인스턴스의 프로퍼티로 등록한다.

2. 프로토타입 체인을 이용하여 `Circle` 생성자에 `getDiameter` 메서드를 등록한다. 여기서도 여전히 어떤 인스턴스를 가리키는지 모르는 상황이다.

3. `circle`이라는 인스턴스에 `Circle` 생성자 함수를 이용하여 객체를 등록한다. 이때 비로소 객체 내부 프로퍼티와 메서드들이 자신이 속한 객체(인스턴스)가 무엇인지 알게 된다.

생성자 함수에서 프로퍼티 또는 메서드를 추가하기 위해서는 생성자 함수 자신이 생성할 인스턴스를 참조할 수 있어야 한다.

하지만 생성자 함수를 정의하는 시점에는 아직 인스턴스가 생성되기 이전이므로, 생성자 함수가 생성할 인스턴스를 가리키는 식별자를 알 수가 없게 된다.

따라서 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 특수한 식별자가 필요하다.

즉, 생성자 함수를 실제 인스턴스에 할당하기 전까지는 자신이 생성하는 인스턴스가 어떤 것인지 알 수 없는 상태이다. 이때 어떻게 해줘야 할까?

## `this`

객체의 프로퍼티와 메서드는 자신이 속한 객체의 프로퍼티를 참조하고 변경할 수 있어야 한다.
이때 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다. 즉, 메서드는 자기 자신이 속한 객체가 무엇인지 참조할 수 있어야 한다.

이를 위해 자바스크립트에서는 `this`라는 특수한 키워드를 제공한다.

`this`는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 **자기 참조 변수**<sup>self-referencing bvariable</sup>다.

`this`를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.

바로 위의 예제 코드에 `???`라고 되어 있던 자기 참조 변수에 `this`를 할당하게 되면, `Circle` 함수가 생성할 인스턴스를 가리키게 된다. 즉, 생성할 인스턴스 식별자를 가변적으로 `this`로 할당시킬 수가 있게 된다.

```js
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  return 2 * this.radius;
};

// this = circle1
const circle1 = new Circle(5);

// this = circle2
const circle2 = new Circle(10);
```

`this`는 자바스크립트 엔진에 의해 암묵적으로 생성되고, 코드 어디서는 참조할 수 있다.

함수를 호출하면 `arguments` 객체와 `this`가 암묵적으로 함수 내부에 전달된다. 함수 내부에서 `arguments` 객체를 지역 변수처럼 사용할 수 있는 것처럼, `this`도 지역 변수처럼 사용할 수 있다.

```js
function greet(name) {
  console.log(`Hello, ${name}!`);
  console.log(arguments);
  console.log(this);
}

greet("World");

// Hello, World!
// ['World']
// Window (Node.js에서는 Global)
```

### thisBinding

`this`가 가리키는 값을 결정하는 것을 **this 바인딩**이라고 한다.

바인딩이란 식별자와 값을 연결하는 과정을 의미하는데, this 바인딩은 `this`와 `this`가 가리킬 객체를 바인딩하는 것이다.

`this`는 키워드로 분류되지만, 실행 컨텍스트 내에서 식별자 역할을 하게 된다. 위에서 살펴본 객체 리터럴과 생성자 함수의 예제를 `this`를 사용해서 수정해보자.

#### 1) 객체 리터럴

객체 리터럴의 메서드 내부에서의 `this`는 메서드를 호출한 객체, 즉 `circle`을 가리킨다.

```js
const circle = {
  radius: 5,
  getDiameter() {
    // this는 메서드를 호출한 객체
    return 2 * this.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

#### 2) 생성자 함수

생성자 함수 내부의 `this`는 생성자 함수가 생성할 인스턴스를 가리킨다.

```js
function Circle(radius) {
  // this는 생성자 함수가 생성할 인스턴스
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // this는 생성자 함수가 생성할 인스턴스
  return 2 * this.radius;
};

const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

이처럼 `this`는 상황에 따라 가리키는 대상이 다르다.

자바나 C++ 같은 클래스 기반 언어에서는 `this`는 언제나 클래스가 생성하는 인스턴스를 가리킨다. 하지만 자바스크립트의 `this`는 함수가 호출되는 방식에 따라 `this`에 바인딩될 값, 즉 `this` 바인딩이 동적으로 결정된다.

## 실행 컨텍스트와 `this` 바인딩

자바스크립트에서 실행 컨텍스트(Execution Context) 는 코드 실행 환경을 나타내며, 함수나 코드 블록이 실행될 때마다 생성된다. 이 실행 컨텍스트는 코드가 실행될 때 `this` 바인딩, 변수 스코프, 클로저 등을 결정하는 중요한 역할을 한다.

실행 컨텍스트는 함수 호출 시마다 새롭게 생성되며, 그 안에서 **this**는 어떻게 호출되는지에 따라 다르게 바인딩된다.

실행 컨텍스트 내에서 **this**가 바인딩되는 방식은 함수 호출 방식에 따라 다르다. 각 호출 방식에 따라 `this`의 값이 다르게 결정되는데, 이 결정은 **실행 컨텍스트가 생성되는 과정**에서 이루어진다.

### 1. 글로벌 실행 컨텍스트

자바스크립트에서 가장 기본적인 실행 컨텍스트는 **글로벌 실행 컨텍스트(Global Execution Context)**다. 자바스크립트 파일이 열릴 때 생성되며, 전역에서 실행되는 모든 코드에 대한 실행 컨텍스트다. 이때 `this`는 글로벌 객체를 가리킨다.

```js
console.log(this); // 브라우저에서는 window, Node.js에서는 global
```

### 2. 함수 실행 컨텍스트

함수가 호출될 때마다 새로운 실행 컨텍스트가 생성된다. 함수가 호출될 때마다 함수 실행 컨텍스트가 컨텍스트 스택에 쌓이고, 함수가 실행되면서 **this**는 그 함수의 호출 방식에 따라 결정된다.

함수 호출 방식의 구분은 다음과 같다.

1. 일반 함수 호출
2. 메서드 호출
3. 생성자 함수 호출
4. `apply`, `call`, `bind`를 통한 간접 호출

### 1) 일반 함수 호출

일반 함수 호출에서는 `this`가 글로벌 객체를 가리킨다.

- 브라우저 환경: `window` 객체
- Node.js: `global` 객체

자바스크립트에서 함수가 일반적으로 호출될 때 `this`는 호출한 객체가 없으므로 글로벌 객체를 참조하게 된다.

```js
function greet(name) {
  console.log(`Hello, ${name}!`);
  console.log(this);
}

greet("World");
// Hello, World!
// Window (Node.js에서는 Global)
```

### 2) 메서드 호출

메서드 호출에서는 `this`가 메서드를 호출한 객체를 가리킨다. 즉, 해당 메서드가 속한 객체를 가리키게 된다.

```js
const obj = {
  name: "Alice",
  greet() {
    console.log(`Hello, ${this.name}!`);
  },
};

obj.greet(); // Hello, Alice!
```

### 3) 생성자 함수 호출

생성자 함수 호출에서는 `this`가 생성된 객체를 가리킨다. `new` 키워드를 사용하여 생성자 함수를 호출하면 그 내부의 `this`는 새로 생성된 객체를 가리킵니다. 객체의 초기화 및 메서드를 호출할 때 주로 사용된다.

```js
function Person(name) {
  this.name = name;
}

const person = new Person("Alice");
console.log(person.name); // Alice
```

### 4) `apply`, `call`, `bind`에 의한 간접 호출

`apply`, `call`, `bind` 메서드를 사용할 때 `this`는 첫 번째 인자로 전달된 객체로 바인딩된다. 이 메서드들은 함수를 간접적으로 호출할 수 있게 해주며, `this`를 명시적으로 지정할 수 있다.

```js
function greet() {
  console.log(this.name);
}

const person = {
  name: "Bob",
};

greet.call(person); // Bob
greet.apply(person); // Bob

const boundGreet = greet.bind(person);
boundGreet(); // Bob
```

위 코드에서 `greet.call(person)`과 `greet.apply(person)`는 `this`를 `person` 객체로 바인딩하여 `name`을 출력하게 한다. `bind` 메서드도 동일한 방식으로 `this`를 지정하여 `boundGreet()`가 호출될 때 `this`가 `person`을 가리키도록 한다.

### 5) 화살표 함수에서의 this 바인딩

화살표 함수는 자체적인 `this` 바인딩을 갖지 않고 상위 스코프의 `this`를 그대로 참조한다. 화살표 함수가 생성될 때 `this`를 렉시컬하게 바인딩하기 때문이다.

즉, 화살표 함수는 자신이 실행되는 시점이 아니라 정의된 시점의 `this` 값을 그대로 사용한ㄷ다. 그래서 화살표 함수 내에서 `this`를 참조하면 화살표 함수가 정의된 상위 스코프에서의 `this` 값을 참조하게 되는 것이다.

```js
const person = {
  name: "Alice",
  greet() {
    setTimeout(() => {
      console.log(this.name);
    }, 1000);
  },
};

person.greet(); // Alice
```

위 코드에서 setTimeout 내의 화살표 함수는 **상위 스코프의 this**를 참조하기 때문에, `this.name`은 `person` 객체의 `name을` 참조하게 되어 'Alice'가 출력된다.

현재 코드에서는 중첩 객체, 즉 메서드가 존재하기 때문에 `this`를 활용할 수 있지만 일반적으로 `this`를 사용해야 하는 경우에는 화살표 함수를 잘 활용하지 않는다.

## this 바인딩의 특이한 점: 클래스와 this

자바스크립트에서 클래스를 정의할 때도 `this`의 동작은 `this` 바인딩 규칙에 따라 다르다. 클래스 내의 메서드에서 `this`는 인스턴스 객체를 가리킨다. 하지만 클래스 외부에서 메서드를 호출할 경우, `this`는 `undefined` 또는 글로벌 객체가 될 수 있다.

```js
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(this.name);
  }
}

const bob = new Person("Bob");
bob.greet(); // Bob

const greetFn = bob.greet;
greetFn(); // undefined (엄격 모드에서는 오류 발생)
```

위 코드에서 `bob.greet()`는 `this`가 `bob` 객체를 가리키기 때문에 'Bob'이 출력된다. 하지만 `greetFn()`을 호출하면 `this`가 `undefined`로 바인딩되어 예상과 다른 결과를 낳는다.
