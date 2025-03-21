# 모듈과 모듈화

- **모듈화(Modularization)**: 각각의 관심사로 파일을 분리하는 것
- **모듈(Module)**: 분리된 파일들

ES6부터 모듈화를 지원하는 표준 문법이 등장하여 대부분 프로그램에서 이 표준화된 모듈 문법을 사용한다.

## 모듈 스코프 (Module Scope)

- 한 모듈에서 선언한 변수나 함수가 다른 모듈에서 직접 접근할 수 없도록 모든 **모듈은 독립적인 스코프를 가져야 함**

- 모듈을 다른 모듈에서 사용하려면 `export`와 `import`를 사용해야 하고, 모듈을 `export` 해주지 않으면 외부에서 접근이 불가능

### 모듈 스코프 만들어주기

- `<script>` 태그 속성에 `type='module'`을 지정

```html
<body>
  ...
  <script type="module" src="index.js"></script>
</body>
```

- 모듈 스코프를 적용 시, 자바스크립트 모듈 문법 자체의 보안 요구사항 때문에 **브라우저에서 바로 html 파일을 불러오면 다음과 같은 오류 발생**

![모듈 스코프 오류](./imgs/module-scope-error.png)

- 자바스크립트 모듈 파일은 서버를 통해 HTML 파일을 실행해야함
- 가장 간단하게 이용할 수 있는 방법은 **"Live Server" 익스텐션**을 사용하는 것

## 모듈 문법

### 1. export

- 모듈의 변수나 함수를 외부로 내보낼 때 사용
- `export` 시킨 코드에 대해서 다른 모듈에서 접근할 수 있게 됨

```js
// module1.js

export const string = "Hello World!";

export function print(value) {
  console.log(value);
}
```

### 2. import

- `export`된 변수나 함수를 다른 파일에서 사용할 때 사용

```js
// module2.js

import { string, print } from "./module1.js";

print(string); // Hello World!
```

### 3. Aliasing

- 모듈 변수나 함수의 이름에 `as` 키워드를 사용해서 별명을 붙일 수 있다.

#### 1) export에서

```js
const string = "Hello World!";

export { string as greeting };
```

#### 2) import에서

```js
import { string as greeting } from "./module1.js";
```

### 4. Named Export

- 여러 대상의 이름으로 한 번에 내보내고 불러올 수 있다.

#### 1) 한 번에 내보내기

```js
const string = "Hello World!";

function print(value) {
  console.log(value);
}

// 한 번에 내보내기
export { string, print };
```

#### 2) 한 번에 불러오기

- 이때 사용하는 '\*' 문자는 "와일드 카드 문자"라고 함

```js
// 한 번에 불러오고(*) 에일리어싱
import * as printer from "./module1.js";

// 객체 형식으로 사용한다
printer.print(string);
```

### 5. Default Export

- `export default`
- 한 모듈에서 단 하나의 값(변수, 함수 등)을 내보낼 때 사용
- `import` 시 중괄호 없이 원하는 이름으로 사용
- 한 모듈에서 `export default` 2번 이상 사용하면 오류 발생
- 외부 파일에서 `default` 키워드로 구분됨 (에일리어싱 필수)

```js
// 한 파일에 default 하나
export default function print(value) {
  console.log(value);
}
```

```js
import print from "./module1.js";

print("Hello world!");
```
