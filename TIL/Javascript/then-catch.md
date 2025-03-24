# 자바스크립트 비동기 프로그래밍 (4)

## 1. `.then` 메서드

- 프로미스 객체의 메서드
- 앞선 비동기 작업이 끝나면 등록된 콜백 실행
- 프로미스 뒤에 메서드를 계속 연결해서 쓰는 것을 **Promise Chain**이라고 함

```js
fetch("https://...")
  .then((response) => reponse.json())
  .then((data) => console.log(data));
```
