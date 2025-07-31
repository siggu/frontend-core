---
sidebar_position: 3
---

# var, let, const

JavaScript에서 변수를 선언하는 방법에는 var, let, const가 있다. 각각은 스코프, 호이스팅, 재선언/재할당 규칙에서 서로 다른 특성을 가지며, ES6(ES2015)에서 let과 const가 도입되면서 var의 문제점들을 해결하고 더 안전한 코드 작성이 가능해졌다.

## 변수 선언 키워드 개요

### var (ES5 이전부터 존재)

var는 JavaScript 초기부터 존재했던 변수 선언 키워드다:

```javascript
var name = '홍길동';
var age = 25;
var isStudent = true;

console.log(name); // '홍길동'
console.log(age); // 25
console.log(isStudent); // true
```

### let (ES6/ES2015 도입)

let은 블록 스코프를 가지는 변수 선언 키워드다:

```javascript
let name = '홍길동';
let age = 25;
let isStudent = true;

// 값 변경 가능
name = '김철수';
age = 30;

console.log(name); // '김철수'
console.log(age); // 30
```

### const (ES6/ES2015 도입)

const는 상수(constant)를 선언하는 키워드다:

```javascript
const PI = 3.14159;
const MAX_COUNT = 100;
const USER_NAME = '홍길동';

console.log(PI); // 3.14159
console.log(MAX_COUNT); // 100

// PI = 3.14; // TypeError: Assignment to constant variable.
```

<br></br>

## 스코프 (Scope) 차이

### var의 함수 스코프

var는 **함수 스코프**를 가진다. 함수 내부에서 선언된 var 변수는 함수 전체에서 접근 가능하다:

```javascript
function example() {
  if (true) {
    var x = 1;
    console.log(x); // 1
  }

  console.log(x); // 1 - 블록 밖에서도 접근 가능

  for (var i = 0; i < 3; i++) {
    var y = i;
  }

  console.log(i); // 3 - 루프 밖에서도 접근 가능
  console.log(y); // 2 - 루프 밖에서도 접근 가능
}

example();
```

**전역 스코프에서의 var:**

```javascript
var globalVar = '전역 변수';

function test() {
  console.log(globalVar); // '전역 변수' - 접근 가능
}

console.log(globalVar); // '전역 변수'
test();
```

### let과 const의 블록 스코프

let과 const는 **블록 스코프**를 가진다. 중괄호 `{}` 안에서 선언된 변수는 해당 블록 내부에서만 접근 가능하다:

```javascript
function example() {
  if (true) {
    let x = 1;
    const y = 2;
    console.log(x); // 1
    console.log(y); // 2
  }

  // console.log(x); // ReferenceError: x is not defined
  // console.log(y); // ReferenceError: y is not defined

  for (let i = 0; i < 3; i++) {
    let z = i;
    console.log(i, z); // 0 0, 1 1, 2 2
  }

  // console.log(i); // ReferenceError: i is not defined
  // console.log(z); // ReferenceError: z is not defined
}

example();
```

**블록 스코프 예시:**

```javascript
// 블록별로 독립적인 스코프
{
  let blockVar = '블록 1';
  console.log(blockVar); // '블록 1'
}

{
  let blockVar = '블록 2'; // 같은 이름이지만 다른 변수
  console.log(blockVar); // '블록 2'
}

// console.log(blockVar); // ReferenceError: blockVar is not defined
```

### 스코프 차이가 중요한 이유

**클로저와 이벤트 핸들러에서의 차이:**

```javascript
// var 사용시 문제점
function createButtons() {
  for (var i = 0; i < 3; i++) {
    setTimeout(() => {
      console.log('버튼 ' + i + ' 클릭'); // 모두 "버튼 3 클릭" 출력
    }, 100);
  }
}

// let 사용시 해결
function createButtonsFixed() {
  for (let i = 0; i < 3; i++) {
    setTimeout(() => {
      console.log('버튼 ' + i + ' 클릭'); // "버튼 0 클릭", "버튼 1 클릭", "버튼 2 클릭"
    }, 100);
  }
}

createButtons();
createButtonsFixed();
```

<br></br>

## 호이스팅 (Hoisting) 차이

### 호이스팅이 발생하는 이유

호이스팅이 발생하는 주요 이유는 **JavaScript 엔진의 실행 컨텍스트 생성 과정** 때문입니다.

JavaScript 코드 실행 시:

1. **생성 단계(Creation Phase)**:

   - 변수와 함수 선언을 메모리에 먼저 등록
   - var는 `undefined`로 초기화
   - let/const는 초기화하지 않음 (TDZ 상태)
   - 함수 선언은 완전히 등록

2. **실행 단계(Execution Phase)**:
   - 코드를 위에서 아래로 실행

이렇게 선언부를 먼저 처리하는 이유는:

- **상호 참조 함수** 허용 (함수 A가 함수 B를 호출하고, B가 A를 호출)
- **스코프 체인** 미리 구성
- **메모리 할당** 최적화

결국 호이스팅은 JavaScript의 **실행 컨텍스트 생성 방식**에서 자연스럽게 발생하는 현상입니다.

### var의 호이스팅

var로 선언된 변수는 **선언부가 함수/전역 스코프의 최상단으로 끌어올려진다**:

```javascript
console.log(x); // undefined (에러가 아님!)
var x = 5;
console.log(x); // 5

// 위 코드는 실제로 다음과 같이 동작함:
// var x; // 선언만 호이스팅됨
// console.log(x); // undefined
// x = 5; // 할당은 원래 위치에서 실행
// console.log(x); // 5
```

**함수 내부에서의 var 호이스팅:**

```javascript
function example() {
  console.log(name); // undefined

  if (false) {
    var name = '홍길동'; // 실행되지 않지만 선언은 호이스팅됨
  }

  console.log(name); // undefined
}

example();

// 실제 동작:
function example() {
  var name; // 호이스팅됨
  console.log(name); // undefined

  if (false) {
    name = '홍길동'; // 실행되지 않음
  }

  console.log(name); // undefined
}
```

### let과 const의 호이스팅

let과 const도 호이스팅되지만 **Temporal Dead Zone(TDZ)** 때문에 선언 전에 접근하면 에러가 발생한다:

```javascript
// console.log(x); // ReferenceError: Cannot access 'x' before initialization
let x = 5;
console.log(x); // 5

// console.log(y); // ReferenceError: Cannot access 'y' before initialization
const y = 10;
console.log(y); // 10
```

**Temporal Dead Zone (TDZ):**

```javascript
function example() {
  // TDZ 시작
  // console.log(name); // ReferenceError
  // console.log(age); // ReferenceError

  let name = '홍길동'; // TDZ 종료 (name)
  const age = 25; // TDZ 종료 (age)

  console.log(name); // '홍길동'
  console.log(age); // 25
}
```

**블록 스코프에서의 TDZ:**

```javascript
let x = 1;

{
  // console.log(x); // ReferenceError (TDZ)
  let x = 2; // 블록 내부의 x
  console.log(x); // 2
}

console.log(x); // 1 (외부 x)
```

<br></br>

## 재선언과 재할당 규칙

### var의 재선언과 재할당

var는 **재선언과 재할당 모두 가능**하다:

```javascript
var name = '홍길동';
console.log(name); // '홍길동'

// 재할당 가능
name = '김철수';
console.log(name); // '김철수'

// 재선언 가능
var name = '이영희';
console.log(name); // '이영희'

// 같은 스코프에서 여러 번 재선언 가능
var name = '박민수';
var name = '최지영';
console.log(name); // '최지영'
```

### let의 재선언과 재할당

let은 **재할당은 가능하지만 재선언은 불가능**하다:

```javascript
let name = '홍길동';
console.log(name); // '홍길동'

// 재할당 가능
name = '김철수';
console.log(name); // '김철수'

// 재선언 불가능
// let name = '이영희'; // SyntaxError: Identifier 'name' has already been declared
```

**블록 스코프가 다르면 재선언 가능:**

```javascript
let x = 1;

{
  let x = 2; // 다른 스코프이므로 가능
  console.log(x); // 2
}

console.log(x); // 1
```

### const의 재선언과 재할당

const는 **재선언과 재할당 모두 불가능**하다:

```javascript
const name = '홍길동';
console.log(name); // '홍길동'

// 재할당 불가능
// name = '김철수'; // TypeError: Assignment to constant variable.

// 재선언 불가능
// const name = '이영희'; // SyntaxError: Identifier 'name' has already been declared
```

**const는 선언과 동시에 반드시 초기화해야 함:**

```javascript
// const x; // SyntaxError: Missing initializer in const declaration
const x = 10; // 올바른 사용
```

**객체와 배열의 경우:**

```javascript
// 객체의 내부 속성은 변경 가능
const user = {
  name: '홍길동',
  age: 25,
};

user.name = '김철수'; // 가능
user.age = 30; // 가능
console.log(user); // { name: '김철수', age: 30 }

// user = {}; // TypeError: Assignment to constant variable.

// 배열의 내부 요소는 변경 가능
const numbers = [1, 2, 3];
numbers.push(4); // 가능
numbers[0] = 10; // 가능
console.log(numbers); // [10, 2, 3, 4]

// numbers = []; // TypeError: Assignment to constant variable.
```

<br></br>

## 핵심 차이점 비교표

| 특성               | var                         | let             | const           |
| ------------------ | --------------------------- | --------------- | --------------- |
| **스코프**         | 함수 스코프                 | 블록 스코프     | 블록 스코프     |
| **호이스팅**       | 선언부 호이스팅 (undefined) | TDZ로 접근 불가 | TDZ로 접근 불가 |
| **재선언**         | 가능                        | 불가능          | 불가능          |
| **재할당**         | 가능                        | 가능            | 불가능          |
| **초기화**         | 선택사항                    | 선택사항        | 필수            |
| **전역 객체 속성** | 추가됨                      | 추가 안됨       | 추가 안됨       |

### 전역 객체 속성 차이

```javascript
// 브라우저 환경에서
var globalVar = 'var로 선언';
let globalLet = 'let으로 선언';
const globalConst = 'const로 선언';

console.log(window.globalVar); // 'var로 선언'
console.log(window.globalLet); // undefined
console.log(window.globalConst); // undefined
```

<br></br>

## 실제 개발에서의 활용

### 반복문에서의 차이

**var 사용시 문제점:**

```javascript
// 문제가 있는 코드
for (var i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log('var:', i); // 모두 3 출력
  }, 100 * i);
}

// 해결책 1: let 사용
for (let i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log('let:', i); // 0, 1, 2 출력
  }, 100 * i);
}

// 해결책 2: IIFE 사용 (var로 해결)
for (var i = 0; i < 3; i++) {
  (function (index) {
    setTimeout(() => {
      console.log('IIFE:', index); // 0, 1, 2 출력
    }, 100 * index);
  })(i);
}
```

### 이벤트 핸들러에서의 차이

```javascript
// HTML: <button id="btn1">버튼 1</button> <button id="btn2">버튼 2</button> <button id="btn3">버튼 3</button>

// var 사용시 문제
function addEventListenersVar() {
  for (var i = 1; i <= 3; i++) {
    document.getElementById('btn' + i).addEventListener('click', function () {
      alert('버튼 ' + i + ' 클릭'); // 모두 "버튼 4 클릭"
    });
  }
}

// let 사용시 해결
function addEventListenersLet() {
  for (let i = 1; i <= 3; i++) {
    document.getElementById('btn' + i).addEventListener('click', function () {
      alert('버튼 ' + i + ' 클릭'); // "버튼 1 클릭", "버튼 2 클릭", "버튼 3 클릭"
    });
  }
}
```

### 모듈 패턴에서의 활용

```javascript
// 즉시 실행 함수와 const를 활용한 모듈 패턴
const UserModule = (function () {
  // private 변수들
  let users = [];
  const MAX_USERS = 100;

  // private 함수
  function validateUser(user) {
    return user && user.name && user.email;
  }

  // public API 반환
  return {
    addUser: function (user) {
      if (users.length >= MAX_USERS) {
        throw new Error('사용자 수 한도 초과');
      }

      if (!validateUser(user)) {
        throw new Error('잘못된 사용자 정보');
      }

      users.push(user);
    },

    getUserCount: function () {
      return users.length;
    },

    getUsers: function () {
      return [...users]; // 복사본 반환
    },
  };
})();

// 사용
UserModule.addUser({ name: '홍길동', email: 'hong@example.com' });
console.log(UserModule.getUserCount()); // 1
```

### 상수 정의에서의 활용

```javascript
// 설정 상수들
const CONFIG = {
  API_BASE_URL: 'https://api.example.com',
  MAX_RETRY_COUNT: 3,
  TIMEOUT_MS: 5000,

  // 중첩된 객체도 참조는 변경 불가
  CACHE_SETTINGS: {
    TTL: 3600,
    MAX_SIZE: 1000,
  },
};

// CONFIG = {}; // TypeError: Assignment to constant variable.
// CONFIG.API_BASE_URL = 'other'; // 하지만 내부 속성은 변경 가능

// Object.freeze로 완전히 불변 만들기
const FROZEN_CONFIG = Object.freeze({
  API_URL: 'https://api.example.com',
  VERSION: '1.0.0',
});

// FROZEN_CONFIG.API_URL = 'other'; // 무시됨 (strict mode에서는 에러)
```

<br></br>

## 최신 개발 패턴과 권장사항

### ESLint 규칙

```javascript
// .eslintrc.js
module.exports = {
  rules: {
    'no-var': 'error', // var 사용 금지
    'prefer-const': 'error', // 재할당되지 않는 변수는 const 사용
    'no-undef': 'error', // 선언되지 않은 변수 사용 금지
    'block-scoped-var': 'error', // var를 블록 스코프처럼 사용하도록 강제
  },
};
```

### TypeScript에서의 활용

```typescript
// 타입과 함께 사용하는 권장 패턴
const userName: string = '홍길동';
let userAge: number = 25;
const userInfo: readonly string[] = ['홍길동', '25', '서울'];

// 인터페이스와 함께
interface User {
  readonly id: number;
  name: string;
  age: number;
}

const user: User = {
  id: 1,
  name: '홍길동',
  age: 25,
};

// user.id = 2; // 에러: readonly 속성
user.name = '김철수'; // 가능
```

### React/Vue에서의 활용

```javascript
// React Hooks
import React, { useState, useEffect } from 'react';

function UserComponent() {
  const [users, setUsers] = useState([]); // const로 선언
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    const fetchUsers = async () => {
      // const로 함수 선언
      setLoading(true);
      try {
        const response = await fetch('/api/users');
        const userData = await response.json();
        setUsers(userData);
      } catch (error) {
        console.error('사용자 조회 실패:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  return <div>{loading ? '로딩 중...' : users.map((user) => <div key={user.id}>{user.name}</div>)}</div>;
}
```

<br></br>

## 자주 묻는 질문 (FAQ)

### Q: 언제 var, let, const 중 무엇을 사용해야 하나요?

**현대적인 JavaScript 개발에서는 다음 원칙을 따르세요:**

1. **기본적으로 const 사용**: 재할당이 필요 없는 모든 변수
2. **재할당이 필요한 경우만 let 사용**: 반복문 카운터, 조건부 할당 등
3. **var는 사용하지 않기**: 레거시 코드를 제외하고는 사용 금지

```javascript
// ✅ 권장 패턴
const userName = '홍길동'; // 변경되지 않는 값
const users = []; // 배열 자체는 재할당하지 않음

let currentPage = 1; // 값이 변경될 수 있는 변수
let userCount; // 나중에 값이 할당될 변수

for (let i = 0; i < 10; i++) {
  // 반복문에서 let 사용
  // ...
}

// ❌ 피해야 할 패턴
var oldStyleVar = 'avoid this'; // var 사용 금지
```

### Q: const로 선언한 객체나 배열을 왜 수정할 수 있나요?

**const는 '재할당'을 방지하는 것이지 '불변성'을 보장하지는 않습니다:**

```javascript
const user = { name: '홍길동' };

// ✅ 가능: 객체의 속성 변경
user.name = '김철수';
user.age = 25;

// ❌ 불가능: 객체 자체를 재할당
// user = { name: '이영희' }; // TypeError

// 완전한 불변성이 필요하다면 Object.freeze 사용
const frozenUser = Object.freeze({ name: '홍길동' });
// frozenUser.name = '김철수'; // 무시됨 (strict mode에서는 에러)

// 깊은 불변성이 필요하다면 라이브러리 사용
// import { freeze } from 'immer';
// const deepFrozenUser = freeze(user);
```

### Q: 호이스팅이 실제로 어떻게 동작하나요?

**호이스팅은 선언부만 끌어올려지고, 할당은 원래 자리에 남습니다:**

```javascript
// 작성한 코드
console.log(a); // undefined
console.log(b); // ReferenceError
console.log(c); // ReferenceError

var a = 1;
let b = 2;
const c = 3;

// 실제 동작 방식 (개념적으로)
var a; // 선언만 호이스팅
// let b; // 호이스팅되지만 TDZ
// const c; // 호이스팅되지만 TDZ

console.log(a); // undefined
console.log(b); // ReferenceError: Cannot access 'b' before initialization
console.log(c); // ReferenceError: Cannot access 'c' before initialization

a = 1; // 할당은 원래 자리에서
let b = 2; // 선언과 할당이 함께
const c = 3; // 선언과 할당이 함께
```

### Q: for 반복문에서 var와 let의 차이를 자세히 설명해주세요.

**var는 함수 스코프이므로 반복문이 끝난 후에도 변수가 살아있습니다:**

```javascript
// var 사용시
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log('var i:', i), 100); // 모두 3 출력
}
console.log('반복문 후 i:', i); // 3

// 위 코드가 실제로 동작하는 방식
var i; // 함수 스코프로 호이스팅
for (i = 0; i < 3; i++) {
  setTimeout(() => console.log('var i:', i), 100); // 클로저가 같은 i를 참조
}

// let 사용시
for (let j = 0; j < 3; j++) {
  setTimeout(() => console.log('let j:', j), 100); // 0, 1, 2 출력
}
// console.log('반복문 후 j:', j); // ReferenceError

// let은 각 반복마다 새로운 바인딩을 생성
// 개념적으로 다음과 같이 동작:
{
  let j = 0;
  if (j < 3) {
    setTimeout(() => console.log('let j:', j), 100);
    {
      let j = 1; // 새로운 바인딩
      if (j < 3) {
        setTimeout(() => console.log('let j:', j), 100);
        // ... 계속
      }
    }
  }
}
```

### Q: 전역 변수 선언시 var와 let/const의 차이가 있나요?

**var는 전역 객체(window)의 속성이 되지만, let/const는 그렇지 않습니다:**

```javascript
// 브라우저 환경에서
var globalVar = '전역 var';
let globalLet = '전역 let';
const globalConst = '전역 const';

console.log(window.globalVar); // '전역 var'
console.log(window.globalLet); // undefined
console.log(window.globalConst); // undefined

// 하지만 모두 전역에서 접근 가능
console.log(globalVar); // '전역 var'
console.log(globalLet); // '전역 let'
console.log(globalConst); // '전역 const'

// 이는 의도치 않은 전역 속성 생성을 방지함
// 예: jQuery와 같은 라이브러리에서 window.$ 덮어쓰기 방지
```

### Q: Temporal Dead Zone(TDZ)이 정확히 무엇인가요?

**TDZ는 let/const 변수가 선언되기 전까지 접근할 수 없는 구간입니다:**

```javascript
function example() {
  // TDZ 시작 - let x, const y에 대해

  console.log(typeof a); // 'undefined' - 선언되지 않은 변수
  // console.log(typeof x); // ReferenceError - TDZ에 있는 변수

  // console.log(x); // ReferenceError: Cannot access 'x' before initialization
  // console.log(y); // ReferenceError: Cannot access 'y' before initialization

  let x = 1; // TDZ 종료 (x에 대해)
  const y = 2; // TDZ 종료 (y에 대해)

  console.log(x); // 1
  console.log(y); // 2
}

// 블록 스코프에서의 TDZ
let x = 'outer';
{
  // console.log(x); // ReferenceError - 내부 x의 TDZ
  let x = 'inner';
  console.log(x); // 'inner'
}
console.log(x); // 'outer'
```

### Q: 성능상 var, let, const 중 어떤 것이 더 좋나요?

**성능 차이는 미미하지만, 일반적인 특성은 다음과 같습니다:**

```javascript
// 성능 테스트 (실제로는 매우 미미한 차이)
console.time('var 테스트');
for (var i = 0; i < 1000000; i++) {
  var x = i;
}
console.timeEnd('var 테스트');

console.time('let 테스트');
for (let i = 0; i < 1000000; i++) {
  let x = i;
}
console.timeEnd('let 테스트');

console.time('const 테스트');
for (let i = 0; i < 1000000; i++) {
  const x = i;
}
console.timeEnd('const 테스트');

// 실제 성능보다는 코드의 안전성과 가독성이 더 중요
// 현대 JavaScript 엔진들이 최적화를 잘 수행함
```

**권장사항: 성능보다는 코드의 의도와 안전성을 우선시하세요:**

1. **가독성**: const/let이 의도를 더 명확히 전달
2. **안전성**: 블록 스코프와 TDZ로 버그 방지
3. **유지보수성**: 예상치 못한 값 변경 방지
4. **최적화**: 모던 엔진들이 const/let을 더 잘 최적화할 수 있음
