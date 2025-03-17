# 📌 Map과 Set
ES2015(ES6)에서 등장한 `Map`과 `Set`은 기존의 객체(Object)와 배열(Array)과 유사하지만, 더 효율적인 데이터 구조를 제공한다.

## 🗂 1. Map - 키/값 저장소

### 1) Map이란?
- **키(key)와 값(value)로 이루어진 데이터 저장소**
- **객체(Object)와 유사**하지만, 더 강력한 기능을 제공
- **키로 문자열뿐만 아니라 숫자, 객체, 함수까지 사용 가능**
- 데이터의 **삽입 순서 유지**

### 2) Map 생성 및 기본 사용법

- `Map()` 생성자 함수를 사용하여 새로운 Map 객체 생성
- Map 전용 메서드를 통해 Map 객체에 값을 추가, 접근, 삭제

```js
const myMap = new Map();

// 값 추가
myMap.set("name", "Alice");
myMap.set(2025, "Year");
myMap.set(true, "Boolean Key");

// 값 가져오기
console.log(myMap.get("name")); // "Alice"
console.log(myMap.get(2025)); // "Year"
console.log(myMap.get(true)); // "Boolean Key"
```

### 3) 주요 메서드

| 메서드                   | 설명                             |
| --------------------- | ------------------------------ |
| `map.set(key, value)` | 키/값 추가                         |
| `map.get(key)`        | 키에 해당하는 값 반환 (없으면 `undefined`) |
| `map.has(key)`        | key가 존재하면 `true`, 없으면 `false`  |
| `map.delete(key)`     | 해당 키 삭제                        |
| `map.clear()`         | Map 객체 내 모든 요소 제거              |
| `map.size`            | 저장된 키-값 쌍 개수 반환 (프로퍼티)         |

#### 예시: 

```js
// Map 생성
const codeit = new Map();

// set 메소드
codeit.set('title', '문자열 key');
codeit.set(2017, '숫자형 key');
codeit.set(true, '불린형 key');

// get 메소드
console.log(codeit.get(2017)); // 숫자형 key
console.log(codeit.get(true)); // 불린형 key
console.log(codeit.get('title')); // 문자열 key

// has 메소드
console.log(codeit.has('title')); // true
console.log(codeit.has('name')); // false

// size 프로퍼티
console.log(codeit.size); // 3

// delete 메소드
codeit.delete(true);
console.log(codeit.get(true)); // undefined
console.log(codeit.size); // 2

// clear 메소드
codeit.clear();
console.log(codeit.get(2017)); // undefined
console.log(codeit.size); // 0
```

- 문자열과 심볼 값만 프로퍼티 네임으로 사용할 수 있는 일반 객체와 달리
  `Map` 객체는 key로 다양한 자료형을 활용할 수 있음 (문자, 숫자, 객체, 함수)
- 프로퍼티 순서를 보장하지 않는 일반 객체와 달리
  `Map` 객체는 `map.set()`을 통해 추가한 순서대로 순서를 보장함

## 2. Set

### 1) Set이란?

- 중복되지 않는 값들의 집합
- 배열(Array)과 비슷하지만, **중복 값을 자동으로 제거**
- 중복 값을 자동으로 처리하면서 데이터 삽입 순서 유지

### 2) Set 생성 및 기본 사용법
- `Set()` 생성자 함수를 통해 새로운 Set 객체를 생성
- Set 전용 메서드를 통해 Set 객체에 값을 추가, 접근, 삭제

```js
const mySet = new Set();

// 값 추가
mySet.add("apple");
mySet.add("banana");
mySet.add("apple"); // 중복 값 추가 시 무시됨!

console.log(mySet); // Set { 'apple', 'banana' }
console.log(mySet.has("apple")); // true
console.log(mySet.size); // 2
```

### 3) 주요 메서드
| 메서드                 | 설명                          |
| ------------------- | --------------------------- |
| `set.add(value)`    | Set 객체에 값 추가                |
| `set.has(value)`    | 값이 존재하면 `true`, 없으면 `false` |
| `set.delete(value)` | 값 제거 (값이 없으면 `false`)       |
| `set.clear()`       | 모든 요소 제거                    |
| `set.size`          | 요소 개수 반환 (프로퍼티)             |

#### 예시:

```js
// Set 생성
const members = new Set();

// add 메소드
members.add('영훈'); // Set(1) {"영훈"}
members.add('윤수'); // Set(2) {"영훈", "윤수"}
members.add('동욱'); // Set(3) {"영훈", "윤수", "동욱"}
members.add('태호'); // Set(4) {"영훈", "윤수", "동욱", "태호"}

// has 메소드
console.log(members.has('동욱')); // true
console.log(members.has('현승')); // false

// size 프로퍼티
console.log(members.size); // 4

// delete 메소드
members.delete('종훈'); // false
console.log(members.size); // 4
members.delete('태호'); // true
console.log(members.size); // 3

// clear 메소드
members.clear();
console.log(members.size); // 0
```

- Set이 중복 값을 허용하지 않는다는 점에서, 기존 배열을 Set으로 변환하여 중복 값을 제거할 수도 있다.

```js
const numbers = [1, 3, 4, 3, 3, 3, 2, 1, 1, 1, 5, 5, 3, 2, 1, 4];
const uniqNumbers = new Set(numbers);

console.log(uniqNumbers); // Set(5) {1, 3, 4, 2, 5}
```
