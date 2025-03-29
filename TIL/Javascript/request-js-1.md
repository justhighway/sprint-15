# 자바스크립트로 리퀘스트 보내기 (1)

## 1. AJAX

- **A**synchronous **J**avaScript **A**nd **X**ML
- AJAX는 웹 페이지를 새로고침하지 않고도 **서버와 비동기적으로 데이터를 주고받는 기술**
- 과거에는 주로 XML 형식을 사용했기 때문에 “AJAX”라는 이름이 붙었지만, 현재는 JSON을 활용하는 것이 일반적

### 📌 AJAX를 사용하는 이유

#### 페이지 새로고침 없이 서버와 데이터 교환 가능

- 전통적인 방식에서는 서버에 요청을 보낼 때마다 페이지가 새로고침되어야 했음
- AJAX를 사용하면 페이지를 유지한 채로 필요한 데이터만 주고받을 수 있음

#### 비동기 처리를 통한 성능 및 사용자 경험 향상

- 서버 응답을 기다리느라 웹 페이지가 멈추는 현상을 방지하고, 사용자에게 더 부드러운 경험을 제공

#### 서버 리소스 절약

- 페이지 전체를 다시 로드하는 대신, 필요한 데이터만 가져옴
- 때문에 트래픽을 줄이고 서버 부하를 감소시킬 수 있습니다.

## 2. AJAX 주요 기술

### 1) XMLHttpRequest

- AJAX의 가장 초기 기술로, HTTP 요청을 비동기적으로 보내고 응답을 받을 수 있도록 설계된 객체
- GET, POST 요청을 포함한 다양한 HTTP 요청을 지원하며, 데이터 수신 후 특정 동작을 수행할 수 있다.

### 특징

- 가장 오래된 AJAX 기술
- 비동기 요청을 보낼 수 있으나 코드가 복잡하고 사용하기 어려움
- `onreadystatechange`를 사용하여 상태 변화를 감지해야 함

### 사용 예시 (GET 요청)

```js
const xhr = new XMLHttpRequest();
xhr.open("GET", "https://jsonplaceholder.typicode.com/posts/1", true); // true: 비동기 처리
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log(JSON.parse(xhr.responseText));
  }
};
xhr.send();
```

하지만 위 방식은 **콜백 지옥**이 발생하기 쉬우므로, 유지보수가 어려운 단점이 있습니다.

### 2) fetch

- Promise 기반의 API로, XMLHttpRequest보다 더 직관적이고 사용하기 간편한 방식
- 응답을 Promise 형태로 처리할 수 있어 비동기 코드가 더욱 깔끔해짐

### 특징

- `Promise` 기반이므로 `async`/`await`과 함께 사용 가능
- JSON 데이터를 자동 변환하지 않기 때문에 `response.json()`을 호출해야 함
- 기본적으로 실패한 요청에 대해 자동으로 reject되지 않으므로, `response.ok`를 반드시 체크해야 함

### 사용 예시 (GET 요청)

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then((response) => {
    if (!response.ok) {
      throw new Error("Network response was not ok");
    }
    return response.json();
  })
  .then((data) => console.log(data))
  .catch((error) => console.error("Fetch error:", error));
```

### ✅ 사용 예시 (POST 요청)

```js
fetch("https://jsonplaceholder.typicode.com/posts", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    title: "New Post",
    body: "This is a new post",
    userId: 1,
  }),
})
  .then((response) => response.json())
  .then((data) => console.log("Created:", data))
  .catch((error) => console.error("Error:", error));
```

🔹 `fetch()`는 XHR보다 코드가 간결하고 가독성이 좋다는 장점이 있지만, 모든 상황에서 완벽한 것은 아님

### ✅ fetch의 단점

1. fetch()는 HTTP 오류(404, 500 등)를 자동으로 reject하지 않음 → response.ok로 확인 필요

2. fetch()는 기본적으로 타임아웃 기능이 없음 → 수동으로 설정해야 함

3. 브라우저에서만 동작하며, Node.js 환경에서는 직접 사용할 수 없음 → node-fetch 같은 라이브러리 필요

### 3) axios

- fetch보다 더욱 강력한 기능을 제공하는 비동기 HTTP 요청 라이브러리
- Promise 기반으로 동작하며, JSON 변환을 자동으로 수행
- 요청과 응답을 쉽게 처리할 수 있는 인터셉터 기능을 제공

### ✅ 특징

- `fetch`보다 더 사용하기 편리하고 기능이 풍부
- 자동으로 JSON 데이터를 변환해 주므로 `response.json()`을 호출할 필요 없음
- 요청 및 응답을 **인터셉트(가로채서 처리)**할 수 있어 공통적인 요청 로직을 쉽게 정의 가능
- 기본적으로 타임아웃, 요청 취소 기능을 제공

### ✅ 사용 예시 (GET 요청)

```js
axios
  .get("https://jsonplaceholder.typicode.com/posts/1")
  .then((response) => console.log(response.data))
  .catch((error) => console.error("Axios error:", error));
```

### ✅ 사용 예시 (POST 요청)

```js
axios
  .post("https://jsonplaceholder.typicode.com/posts", {
    title: "New Post",
    body: "This is a new post",
    userId: 1,
  })
  .then((response) => console.log("Created:", response.data))
  .catch((error) => console.error("Error:", error));
```

### ✅ Axios의 추가 기능

#### 1. 요청 인터셉터 / 응답 인터셉터

- 모든 요청 또는 응답을 가로채서 특정 처리를 수행할 수 있음

```js
axios.interceptors.request.use((config) => {
  console.log("요청 전 처리");
  return config;
});
```

#### 2. 타임아웃 설정 가능

```js
axios
  .get("https://example.com", { timeout: 5000 }) // 5초 제한
  .then((response) => console.log(response.data))
  .catch((error) => console.error("Timeout error:", error));
```

#### 3. 동시에 여러 개의 요청 처리 (axios.all)

```js
axios
  .all([
    axios.get("https://jsonplaceholder.typicode.com/posts/1"),
    axios.get("https://jsonplaceholder.typicode.com/posts/2"),
  ])
  .then(
    axios.spread((post1, post2) => {
      console.log(post1.data, post2.data);
    })
  );
```

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

## 4. fetch 예외 처리하기

`fetch`를 사용할 때 발생하는 오류는 크게 두 가지로 나눌 수 있다.

### 1) 네트워크 오류 또는 요청 자체의 문제

- 잘못된 URL, 헤더 정보 오류 등으로 인해 요청 자체가 실패하는 경우
- 이때 fetch는 Promise를 rejected 상태로 반환함

### 2) 잘못된 요청 데이터(쿼리 스트링, 파라미터 등)로 인해 서버 응답이 실패

- 요청은 정상적으로 이루어졌지만, 서버에서 4xx(클라이언트 오류) 또는 5xx(서버 오류) 상태 코드를 반환하는 경우
- 이 경우 `fetch`는 `Promise`를 fulfilled(성공) 상태로 반환하지만, 응답의 `status` 값이 오류 코드(4xx, 5xx)일 수 있음

`try...catch` 문을 사용하면 첫 번째 경우(네트워크 오류)는 잡을 수 있지만, 두 번째 경우(HTTP 상태 코드 오류)는 자동으로 처리되지 않는다.

따라서, 응답 객체의 `res.ok` 속성을 확인하여 **상태 코드가 2xx(성공)가 아닐 경우 명시적으로 오류를 발생**시켜야 한다.

### 예제:

다음과 같이 `getColorSurveys()`를 수정하면, 네트워크 오류뿐만 아니라 잘못된 요청으로 인해 서버가 4xx 또는 5xx 응답을 반환하는 경우도 예외 처리 가능하다.

```js
export async function getColorSurveys(params = {}) {
  const url = new URL("https://...");
  Object.keys(params).forEach((key) => {
    url.searchParams.append(key, params[key]);
  });

  try {
    const res = await fetch(url);

    // 응답 상태 코드가 2xx가 아닐 경우 오류 발생
    if (!res.ok) {
      throw new Error(`요청 실패: ${res.status} ${res.statusText}`);
    }

    const data = await res.json();
    return data;
  } catch (error) {
    console.error("데이터를 불러오는 중 오류 발생:", error);
    throw error; // 호출한 함수에서도 오류를 감지할 수 있도록 다시 던짐
  }
}
```
