# 배열 반복 메서드 (iterate)
- 배열의 요소를 반복하며 작업을 수행하는 iterate 메서드
- 메서드 인자로 콜백함수를 전달하면 배열의 요소를 하나씩 순회하면서 해당 콜백함수를 호출 및 실행
- 배열 메서드의 반복 횟수는 메서드가 실행된 순간의 요소 길이만큼 실행
- 반복하면서 `push()`로 요소 추가해도 무한 루프 빠지지 않음
- 단, `pop()` 하면 배열 개수가 반복 횟수만큼 같아지거나 적어질 때 반복이 끝남

## 1. forEach()
- 배열의 각 요소를 반복 실행하는 메서드
- 배열 단순 반복 작업에 사용
- 원본 배열 수정하지 않음
- **리턴 값 없음 (undefined)** → `push()` 등 추가 동작 필요

```js
array.forEach(callback(currentValue, [currentIndex], [array]))
```

### forEach() 예시: 배열 요소들의 2배수 받기

```js
const numbers = [1, 2, 3];
const doubled = [];

numbers.forEach(num => doubled.push(num * 2));

console.log(doubled); // [2, 4, 6]
console.log(numbers); // [1, 2, 3] (원본 배열 변경 X)
```

## 2. map()

- 배열의 각 요소를 변형하여 새로운 배열을 생성하는 메서드
- 배열의 요소를 가공하거나 새로운 배열이 필요할 때 사용
- 원본 배열 수정하지 않음
- **반복 작업 수행한 새로운 배열 반환**

```js
array.map(callback(currentValue, [currentIndex], [array]))
```

### map() 예시: 배열 요소들의 2배수 받기

```js
const numbers = [1, 2, 3];
const doubled = numbers.map(num => num * 2);

console.log(doubled); // [2, 4, 6]
console.log(numbers); // [1, 2, 3] (원본 배열 변경 X)
```

## 3. filter()

- 배열 각 요소에 대하여 콜백함수의 조건식에 만족하는지 검사하는 메서드
- 원본 배열 수정하지 않음
- 콜백함수 실행 결과 `true`를 반환하는 메서드만 새로운 배열에 포함
- 리턴문 조건식에 만족한 요소들이 담긴 새로운 배열 반환
- 조건에 맞는 요소가 없으면 빈 배열`[]` 반환
- 배열을 반환하기 때문에 값으로 사용할 때에는 `spread(…)` 사용해서 벗겨줘야 함

```js
array.filter(callback(currentValue, [currentIndex], [array]));
```

### filter() 예시: 배열 내 짝수 고르기

```js
const numbers = [1, 2, 3, 4, 5, 6]
const even = numbers.filter(v => v % 2 === 0);

console.log(even); // [2, 4, 6]
```


## 4. find()

- 콜백함수의 조건식에 만족하는 가장 처음의 요소를 반환하는 메서드
- `filter()`와 다르게 단일 값만 반환
- 원본 배열 수정하지 않음
- 조건식에 만족하는 값을 찾으면 바로 반복을 종료
- 조건식에 만족하는 값이 없으면 `undefined` 반환

```js
array.find(callback(currentValue, [currentIndex], [array]));
```

### find() 예시: 아이디와 휴대폰번호로 이메일 찾기

```js
const data = [
  { userName: '홍길동', phoneNumber: '01012341111', email: 'hong@email.com' },
  { userName: '성춘향', phoneNumber: '01012342222', email: 'sung@email.com' },
  { userName: '이몽룡', phoneNumber: '01012343333', email: 'lee@email.com' },
  { userName: '심청이', phoneNumber: '01012344444', email: 'chung@email.com' },
  { userName: '심봉사', phoneNumber: '01012345555', email: 'bong@email.com' },
];

function findEmail() {
  const nameValue = nameInput.value;
  const phoneValue = phoneInput.value;

  const user = data.find((v)=> {
    return v.userName === nameValue && v.phoneNumber === phoneValue
  });

  const message = user
    ? `${user.userName}님의 이메일은 ${user.email} 입니다.`
    : '이메일을 찾을 수 없습니다. 입력 정보를 다시 확인해 주세요.';

  alert(message);
}
```


## 5. some()

- 콜백함수의 조건식을 만족하는 요소가 1개 이상 있는지 검사하는 메서드
- 있으면 `true`, 없으면 `false` 반환
- 빈 배열 전달 시 `false` 반환

```js
array.some(callback(currentValue, [currentIndex], [array]));
```


## 6. every()

- 배열 내 모든 요소가 콜백함수의 조건식을 만족하는지 검사하는 메서드
- 만족하면 `true`, 만족하지 않으면 `false`
- 빈 배열 전달 시 `true` 반환

```js
array.every(callback(currentValue, [currentIndex], [array]));
```

### every() 예제: 혼자 이름이 다른 이중 스파이 고르기

```js
const spait = [
  { codeName: 'ApplePie', members: ['스파이', '스파이', '스파이', '스파이', '스파이'] },
  { codeName: 'BigBoss', members: ['스파이', '스파이', '스과이', '스파이', '스파이'] },
  { codeName: 'CEO', members: ['스파이', '스파이', '스파이', '습하이', '스파이'] },
  { codeName: 'DeathNote', members: ['스파이', '스파이', '스파이', '스파이', '스파이'] },
  { codeName: 'EarlyBird', members: ['스파이', '스마이', '스파이', '스파이', '스파이'] },
  { codeName: 'Faker', members: ['스파이', '스파이', '스파이', '스파이', '스파이'] },
];

// 각 객체에 실행해줄 콜백함수
function checkSpy(team) {
  const { codeName, members } = team;
  const result = members.every((member) => member === '스파이');
  const message = result
    ? `팀 ${codeName} 에는 이중 스파이가 없습니다.`
    : `[주의!] 팀 ${codeName} 에 이중 스파이가 있습니다!`;

  console.log(message);
}

// spait 객체 배열에 각 요소에 checkSpy 함수 실행
spait.forEach((team) => checkSpy(team));
```

## 7. reduce()

- 배열을 순회하면서 값을 누적(accumulate)해서 하나의 결과값으로 반환하는 메서드
- 배열의 요소를 하나씩 처리하면서 이전 값과 현재 값을 합쳐서 새로운 값으로 만듦


```js
array.reduce(callback(accumulator, currentValue, [currentIndex], [array]), [initialValue])
```


- `accumulator`: 이전 콜백함수의 리턴 값 (누적값)
- `currentValue`: 현재 처리 중인 요소
- `currentIndex`: 현재 처리 중인 요소의 인덱스
- `array`: 현재 처리 중인 배열
- `initialValue`: reduce 메서드를 실행할 때 `accumulator` 파라미터 인자로 전달할 초기 값


> `initialValue`를 지정해주지 않으면 **배열의 0번째 인덱스 값이 전달되고, 콜백함수가 1번째 인덱스 값부터 실행**된다. 따라서 `initivalValue`는 선택 값이지만 예상치 못한 결과를 방지하기 위해 지정해주는 것이 안전하다.

### reduce() 예제: 배열 평균 구하기

```js
const numbers = [10, 20, 30, 40, 50]
const average = numbers.reduce((acc, v, _, arr) => acc + v / arr.length, 0);

console.log(average); // 30
```

#### 작동 과정

1. acc = 0(초기값), v = 10 → **0 + 10 / 5  =  2**
2. acc = 2, v = 20 → **2 + 20 / 5  =  6**
3. acc = 6, v = 30 → **6 + 30 / 5  =  12**
4. acc = 12, v = 40 → **12 + 40 / 5  =  20**
5. acc = 20, v = 50 → **20 + 50 / 5  =  30**

## 8. sort()

- 배열의 요소 정렬
- 아규먼트 전달해주지 않으면 기본적으로 문자열로 변환한 후 유니코드 기준으로 정렬됨
- 숫자를 오름차순 또는 내림차순으로 정렬해주기 위해서는 별도의 비교 함수 사용
- **기존 배열을 수정**하기 때문에 원본 값은 따로 복사해두고 사용해야함

```js
array.sort([callback(currentValue, nextValue)])
```

```js
const letters = ['D', 'C', 'E', 'B', 'A'];
const numbers = [1, 10, 4, 21, 36000];

letters.sort();
numbers.sort();

console.log(letters); // (5) ["A", "B", "C", "D", "E"]
console.log(numbers); // (5) [1, 10, 21, 36000, 4]
```

### 숫자 비교 함수 반환 값
- `sort()` 인자로 `currentValue`와 `nextValue`를 전달하여 두 값을 비교하는 방식으로 정렬할 수 있다.

1. `반환값 < 0 ` : a가 b보다 앞에 있어야 함 
2. `반환값 > 0` : b가 a보다 앞에 있어야 함 
3. `반환값 = 0` : a와 b가 같음 (**위치 변경 없음**)

### 숫자 정렬하기

```js
const numbers = [1, 10, 4, 21, 36000];

// 오름차순 정렬
numbers.sort((a, b) => a - b);
console.log(numbers); // (5) [1, 4, 10, 21, 36000]

// 내림차순 정렬
numbers.sort((a, b) => b - a);
console.log(numbers); // (5) [36000, 21, 10, 4, 1]
```

### sort() 예제: 객체 배열 나이 순으로 정렬하기
```js
const people = [
  { name: "Alice", age: 30 },
  { name: "Bob", age: 20 },
  { name: "Charlie", age: 25 }
];

people.sort((a, b) => a.age - b.age);

console.log(people);
// [
//   { name: "Bob", age: 20 },
//   { name: "Charlie", age: 25 },
//   { name: "Alice", age: 30 }
// ]
```

## 9. reverse()
- 배열의 순서를 뒤집는 메서드
- 별도 파라미터가 존재하지 않음
- 원본 배열을 수정함

```js
const letters = ['a', 'c', 'b'];
const numbers = [421, 721, 353];

letters.reverse();
numbers.reverse();

console.log(letters); // (3) ["b", "c", "a"]
console.log(numbers); // (3) [353, 721, 421]
```