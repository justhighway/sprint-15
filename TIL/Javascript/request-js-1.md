# 자바스크립트로 리퀘스트 보내기 (1)

## 1. AJAX

- **A**synchronous **J**avaScript **A**nd **X**ML
- **비동기적으로 자바스크립트 코드로 서버에 요청을 보내고 응답을 받아오는 기술**
- 과거에는 XML로 데이터를 많이 주고받아 명명되었지만 현재는 JSON 형식이 일반적

### 요청과 응답을 비동기적으로 처리해야 하는 이유?

- 페이지를 새로고침하지 않고 서버와 데이터를 주고받을 수 있게 해줌
- 모든 코드가 동기적으로 실행되면, 서버의 응답을 받아야 하는 등의 시간이 오래 걸리는 작업을 기다리는 동안 웹 페이지가 멈추게 되어 사용자 경험이 나빠짐
- 비동기 처리를 통해 이러한 대기 시간을 피하고, 서버와의 통신 중에도 다른 작업을 진행할 수 있어 전반적인 사용자 경험을 크게 향상시킬 수 있음

### AJAX 기술

#### 1) XMLHttpRequest

- 가장 오래된 AJAX 기술
- 서버와의 비동기 통신을 위해 사용
- 기본적인 HTTP 요청을 보내고 응답을 처리하는 데 사용
- 코드가 복잡하고 사용하기 어려워 다른 대체 기술들이 등장

#### 2) fetch

- Promise 기반의 API
- XMLHttpRequest보다 사용하기 더 간편하고 직관적
- 비동기 처리를 Promise로 다루기 때문에 코드가 깔끔하고 가독성이 좋음
- 데이터를 요청하고 응답을 받을 때까지 기다린 후, 결과를 처리하는 방식으로 비동기 요청 처리 가능

#### 3) axios

- 비동기 HTTP 요청을 쉽게 만들 수 있도록 도와주는 라이브러리
- 응답 데이터를 자동으로 JSON으로 변환하거나, 요청과 응답을 인터셉트할 수 있는 기능 지원
- Promise 기반으로 작동하므로 비동기 처리가 수월하고 다양한 옵션을 제공

## 2. fetch()

### 1) GET

- `fetch` 함수는 기본적으로 GET 요청을 보냄

```js
fetch("https://learn.codeit.kr/api/color-surveys");
```

- `fetch`는 `Promise`를 반환하므로 결과 값을 가져오려면 `await`를 사용해야 함

```js
const res = await fetch("https://learn.codeit.kr/api/color-surveys");
```

- `response` 객체는 다양한 정보를 포함하며, 주로 사용하는 속성은 `status`, `headers`, `body`임

```js
const res = await fetch("https://learn.codeit.kr/api/color-surveys");
const { status, headers } = res;

console.log(status); // 200
console.log(headers); // Headers { ... }
```

- `body` 내용은 `json()` 또는 `text()` 메서드를 사용해야 가져올 수 있음

```js
const data = await res.json(); // JSON 데이터를 JavaScript 객체로 변환
```

### 2) POST

- `fetch`의 두 번째 매개변수에 HTTP 메서드를 지정하여 GET 외의 요청을 보낼 수 있음

```js
fetch("https://learn.codeit.kr/api/color-surveys", { method: "POST" });
```

- POST 요청은 보통 `body`와 `headers`를 함께 전달함

```js
const surveyData = {
  mbti: "ENFP",
  colorCode: "#ABCDEF",
  password: "0000",
};

fetch("https://learn.codeit.kr/api/color-surveys", {
  method: "POST",
  body: JSON.stringify(surveyData),
  headers: {
    "Content-Type": "application/json",
  },
});
```

- `JSON.stringify(obj)`를 사용하여 JavaScript 객체를 JSON 문자열로 변환해야 함

- POST 요청의 응답도 GET과 동일하게 처리 가능

```js
const result = await fetch("https://learn.codeit.kr/api/color-surveys", {
  method: "POST",
  body: JSON.stringify(surveyData),
  headers: {
    "Content-Type": "application/json",
  },
});

const data = await result.json();
```

### 3) PATCH (부분 수정)

- `PATCH` 요청은 기존 데이터의 일부만 수정할 때 사용
- 변경할 필드만 `body`에 포함하여 요청

```js
const updateData = {
  colorCode: "#123456",
};

const response = await fetch("https://learn.codeit.kr/api/color-surveys/1", {
  method: "PATCH",
  body: JSON.stringify(updateData),
  headers: {
    "Content-Type": "application/json",
  },
});

const result = await response.json();
console.log(result);
```

### 4) DELETE (삭제)

- `DELETE` 요청은 서버에서 특정 데이터를 삭제할 때 사용
- `body`가 필요하지 않은 경우가 많음

```js
const response = await fetch("https://learn.codeit.kr/api/color-surveys/1", {
  method: "DELETE",
});

if (response.ok) {
  console.log("삭제 완료");
} else {
  console.log("삭제 실패");
}
```

## 3. API 함수로 만들기

웹 개발을 할 때에는 API 함수를 따로 만들어서 `import`해서 사용한다.

### 1) GET

- `params` 라는 파라미터의 기본 값을 빈 객체(`{}`)로 설정
- `URL` 객체 생성하여 `url` 변수에 저장
- `Object.keys(params)`로 이터러블로 만든 후, 각 요소에 대하여 `url`의 `searchParams`로 등록 (`append`)

```javascript
export async function getColorSurveys(params = {}) {
  const url = new URL("https://learn.codeit.kr/api/color-surveys");
  Object.keys(params).forEach((key) => {
    url.searchParams.append(key, params[key]);
  });

  const res = await fetch(url);
  const data = await res.json();
  return data;
}
```

- `id` 속성을 이용해서 단일 값만 받아오기

```js
export async function getColorSurvey(id) {
  const res = await fetch(`https://learn.codeit.kr/api/color-surveys${id}`);
  const data = await res.json();
  return data;
}
```

### 2) POST

- POST는 본문과 헤더를 적용해주면 된다.

```js
export async function createColorSurvey(body) {
  const result = await fetch("https://learn.codeit.kr/api/color-surveys", {
    method: "POST",
    body: JSON.stringify(body),
    headers: {
      "Content-Type": "application/json",
    },
  });
  const data = await result.json();
  return data;
}
```

### 3) PATCH

- POST처럼 바디와 헤더만 넣어주면 된다.

```js
export async function patchAvatar(id, avatarData) {
  const res = await fetch(`https://learn.codeit.kr/api/avatars/${id}`, {
    method: "PATCH",
    body: JSON.stringify(avatarData),
    headers: {
      "Content-Type": "application/json",
    },
  });
  const data = await res.json();
  return data;
}
```

### 4) DELETE

- `method` 값만 DELETE로 설정해주면 된다.

```js
export async function deleteAvatar(id) {
  const res = await fetch(`https://learn.codeit.kr/api/avatars/${id}`, {
    method: "DELETE",
  });
  const data = await res.json();
  return data;
}
```
