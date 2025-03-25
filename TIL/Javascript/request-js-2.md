# 자바스크립트로 리퀘스트 보내기 (2)

## 1. Axios 문법

### 1) GET 요청

- `axios.get` 메서드를 사용하여 GET 요청을 보낼 수 있다.
- axios도 `Promise`를 반환하므로 `await`을 사용할 수 있다.

#### 기존 fetch 사용 예제

```js
// 기존 함수
export async function getColorSurvey(id) {
  const res = await fetch(`https://learn.codeit.kr/api/color-surveys/${id}`);
  const data = await res.json();
  return data;
}
```

#### axios 사용 예제

```js
// axios 사용
export async function getColorSurvey(id) {
  const res = await axios.get(
    `https://learn.codeit.kr/api/color-surveys/${id}`
  );
  return res.data; // axios res는 바로 JSON으로 파싱됨
}
```

### 2) GET 요청 (쿼리 파라미터 포함)

- 쿼리 파라미터 처리 시 `fetch`를 사용할 경우 URL 객체를 이용하여 직접 문자열을 조작해야 한다.
- axios에서는 `params` 옵션을 사용하면 자동으로 쿼리 스트링을 생성해 준다.

#### 기존 fetch 사용 예제

```js
// 기존 코드
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

#### axios 사용 예제

```js
// axios 사용
export async function getColorSurveys(params = {}) {
  const res = await axios.get("https://learn.codeit.kr/api/color-surveys", {
    params, // params 객체를 전달하면 자동으로 쿼리 스트링 생성
  });
  return res.data;
}
```

### 3) POST

- `axios.post` 메서드를 사용하면 데이터를 본문(body)으로 전달할 수 있다.
- `fetch`를 사용할 경우, JSON 데이터를 `stringify`하고 `Content-Type`을 직접 설정해야 하지만, axios는 이를 자동으로 처리해 준다.

#### 기존 fetch 사용 예제

```js
export async function createColorSurvey(surveyData) {
  const res = await fetch("https://learn.codeit.kr/api/color-surveys", {
    method: "POST",
    body: JSON.stringify(surveyData),
    headers: {
      "Content-Type": "application/json",
    },
  });
  const data = await res.json();
  return data;
}
```

#### axios 사용 예제

```js
export async function createColorSurvey(surveyData) {
  const res = await axios.post(
    "https://learn.codeit.kr/api/color-surveys",
    surveyData
  );
  return res.data;
}
```

## 2. axios instance

- 공통적으로 적용해야 하는 설정(baseURL, timeout, headers 등)이 있다면
- `axios.create()`로 인스턴스를 생성하여 사용할 수 있다.

#### 기존 코드

```js
// 기존 코드
export async function getColorSurvey(id) {
  const res = await axios.get(
    `https://learn.codeit.kr/api/color-surveys/${id}`
  );
  return res.data;
}
```

#### axios.create()를 사용한 코드

```js
// axios instance 적용
import axios from "axios";

const instance = axios.create({
  baseURL: "https://learn.codeit.kr/api",
  timeout: 3000, // 요청 제한 시간 설정 (ms)
});

export async function getColorSurvey(id) {
  const res = await instance.get(`/color-surveys/${id}`);
  return res.data;
}
```

## 3. axios 오류 처리

- axios는 요청 자체가 실패하거나 응답 상태 코드가 실패(4XX, 5XX)를 나타날 때 모두 `Promise`를 reject함
- 요청이 성공하고, 응답 코드가 '2xx'이어야만 `Promise`가 fulfilled가 됨
- `axios` 함수를 `try...catch` 내부에 넣어주면 됨

```js
try {
  const survey = await getColorSurvey(123);
  console.log(survey);
} catch (e) {
  console.log("에러가 발생했습니다!");
  console.log(e.message);
}
```

- 에러 객체의 메시지는 axios가 알아서 설정해줌
- 에러 리스폰스 바디에 있는 메시지를 사용할 수도 있음

```js
try {
  const survey = await getColorSurvey(123);
  console.log(survey);
} catch (e) {
  console.log("에러가 발생했습니다!");
  console.log(e.response.status); // 응답 상태코드
  console.log(e.response.data); // 응답 바디 내용
}
```

- 응답 객체는 응답이 돌아왔을 때만 존재
- 만약 요청 자체가 실패해서 응답이 돌아오지 않으면 응답 객체에 접근이 불가능함
- `status`나 `data` 같은 값을 사용하려면 응답 객체가 존재하는지부터 확인해야 함

```js
try {
  const survey = await getColorSurvey(123);
  console.log(survey);
} catch (e) {
  if (e.response) {
    console.log(e.response.status);
    console.log(e.response.data);
  } else {
    console.log("리퀘스트 실패");
  }
}
```
