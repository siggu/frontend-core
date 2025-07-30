---
sidebar_position: 1
---

# Promise와 Callback의 차이점

비동기 프로그래밍에서 Callback과 Promise는 모두 비동기 작업을 처리하는 방법이지만, 코드의 가독성, 에러 처리, 그리고 유지보수성 면에서 큰 차이를 보인다. 특히 JavaScript 생태계에서 Promise의 도입은 비동기 코드 작성 패러다임의 혁신적인 변화를 가져왔다.

## 비동기 처리란?

비동기 처리는 **"결과를 기다리지 않고 다음 코드를 실행하는"** 프로그래밍 방식이다.

### 동기 vs 비동기 처리

**동기 처리** (**Synchronous**)는 작업이 순차적으로 실행되며, 이전 작업이 완료될 때까지 다음 작업이 대기한다:

```javascript
// 동기 처리 예시
console.log('작업 1 시작');
// 3초 대기 (블로킹)
for (let i = 0; i < 3000000000; i++) {}
console.log('작업 1 완료');

console.log('작업 2 시작');
console.log('작업 2 완료');

// 출력:
// 작업 1 시작
// (3초 후) 작업 1 완료
// 작업 2 시작
// 작업 2 완료
```

**비동기 처리** (**Asynchronous**)는 작업을 백그라운드에서 실행하고, 완료되면 콜백을 통해 결과를 처리한다:

```javascript
// 비동기 처리 예시
console.log('작업 1 시작');
setTimeout(() => {
  console.log('작업 1 완료');
}, 3000);

console.log('작업 2 시작');
console.log('작업 2 완료');

// 출력:
// 작업 1 시작
// 작업 2 시작
// 작업 2 완료
// (3초 후) 작업 1 완료
```

### JavaScript의 비동기 처리 메커니즘

JavaScript는 **싱글 스레드** 언어이지만, **이벤트 루프**(**Event Loop**)와 **Web APIs**를 통해 비동기 처리를 구현한다.

#### JavaScript 실행 환경 구조

**1. JavaScript 엔진**

- **Call Stack**: 함수 호출과 실행 컨텍스트를 관리하는 LIFO(Last In, First Out) 구조
- **Heap**: 객체들이 저장되는 메모리 영역

**2. Web APIs (브라우저 환경)**

- **Timer API**: `setTimeout`, `setInterval`
- **HTTP API**: `fetch`, `XMLHttpRequest`
- **DOM API**: `addEventListener`, DOM 이벤트 처리

**3. Event Loop 시스템**

- **Callback Queue (Task Queue)**: 비동기 콜백들이 대기하는 FIFO 큐
- **Microtask Queue**: Promise, queueMicrotask 등의 마이크로태스크 대기 큐

**실행 환경 관계도:**

```
     JavaScript 엔진            Web APIs              Event Loop
  ┌─────────────────┐       ┌─────────────┐       ┌─────────────────┐
  │   Call Stack    │       │   Timer     │       │ Callback Queue  │
  │                 │       │   HTTP      │       │                 │
  │     Heap        │       │   DOM       │       │ Microtask Queue │
  └─────────────────┘       └─────────────┘       └─────────────────┘
```

**데이터 흐름:**

1. **Call Stack → Web APIs**:

   - `setTimeout()`, `fetch()`, `addEventListener()` 등 비동기 함수 호출 시 해당 작업을 Web API에 위임

2. **Web APIs → Event Loop**:

   - Timer 완료 → Callback Queue에 콜백 추가
   - Promise 완료 → Microtask Queue에 콜백 추가
   - DOM 이벤트 발생 → Callback Queue에 이벤트 핸들러 추가

3. **Event Loop → Call Stack**:
   - Call Stack이 비어있을 때만 큐에서 콜백을 가져와 실행
   - 우선순위: Microtask Queue > Callback Queue

#### 이벤트 루프 동작 과정

```javascript
console.log('1: 시작');

setTimeout(() => {
  console.log('4: setTimeout 콜백');
}, 0);

Promise.resolve().then(() => {
  console.log('3: Promise then');
});

console.log('2: 끝');

// 출력 순서: 1 → 2 → 3 → 4

// 동작 과정:
// 1. console.log('1: 시작') → Call Stack에서 즉시 실행
// 2. setTimeout → Web API로 전달, 0ms 후 Callback Queue에 추가
// 3. Promise.resolve().then() → Microtask Queue에 즉시 추가
// 4. console.log('2: 끝') → Call Stack에서 즉시 실행
// 5. Call Stack이 비면 Event Loop가 Microtask Queue 확인 → Promise then 실행
// 6. Microtask Queue가 비면 Callback Queue 확인 → setTimeout 콜백 실행
```

#### Task vs Microtask 우선순위

```javascript
// Microtask가 Task보다 높은 우선순위를 가짐
console.log('시작');

// Task (Macrotask)
setTimeout(() => console.log('Task 1'), 0);
setTimeout(() => console.log('Task 2'), 0);

// Microtask
Promise.resolve().then(() => console.log('Microtask 1'));
Promise.resolve().then(() => console.log('Microtask 2'));

// Task (Macrotask)
setImmediate(() => console.log('Immediate')); // Node.js에서만 동작

console.log('끝');

// 출력 순서:
// 시작
// 끝
// Microtask 1
// Microtask 2
// Task 1
// Task 2
// Immediate (Node.js)

// 우선순위: Call Stack → Microtask Queue → Callback Queue (Task Queue)
```

#### 실제 비동기 작업 흐름

```javascript
// 실제 DOM 이벤트와 HTTP 요청의 비동기 처리
function demonstrateAsyncFlow() {
  console.log('1. 함수 시작');

  // DOM 이벤트 등록 (Web API)
  document.addEventListener('click', () => {
    console.log('5. 클릭 이벤트 발생');
  });

  // HTTP 요청 (Web API)
  fetch('/api/data')
    .then((response) => {
      console.log('6. HTTP 응답 받음');
      return response.json();
    })
    .then((data) => {
      console.log('7. 데이터 파싱 완료');
    });

  // 타이머 설정 (Web API)
  setTimeout(() => {
    console.log('4. 타이머 완료');
  }, 100);

  // 즉시 실행되는 Promise (Microtask)
  Promise.resolve().then(() => {
    console.log('3. Promise 마이크로태스크');
  });

  console.log('2. 함수 끝');
}

demonstrateAsyncFlow();

// 실행 흐름:
// 1. Call Stack에서 동기 코드 실행 (1, 2)
// 2. 이벤트 리스너는 Web API에 등록
// 3. fetch는 Web API에서 HTTP 요청 시작
// 4. setTimeout은 Web API에서 타이머 시작
// 5. Promise는 Microtask Queue에 추가
// 6. Call Stack이 비면 Microtask Queue 처리 (3)
// 7. 타이머 완료 시 Callback Queue에 추가 후 실행 (4)
// 8. HTTP 응답 시 Promise chain 실행 (6, 7)
// 9. 사용자 클릭 시 이벤트 핸들러 실행 (5)
```

<br></br>

## Callback의 개념과 한계

### Callback 함수란?

Callback은 "**다른 함수의 매개변수로 전달되어, 특정 시점에 호출되는 함수**"다.

```javascript
// 기본 Callback 예시
function processData(data, callback) {
  console.log('데이터 처리 중...');
  setTimeout(() => {
    const result = data.toUpperCase();
    callback(result); // 처리 완료 후 콜백 호출
  }, 1000);
}

processData('hello world', (result) => {
  console.log('결과:', result); // "결과: HELLO WORLD"
});
```

### Callback의 실제 활용

**파일 읽기 예시:**

```javascript
const fs = require('fs');

// Node.js 파일 시스템 API
fs.readFile('data.txt', 'utf8', (err, data) => {
  if (err) {
    console.error('파일 읽기 실패:', err);
    return;
  }
  console.log('파일 내용:', data);
});

console.log('파일 읽기 요청 완료'); // 먼저 출력됨
```

**HTTP 요청 예시:**

```javascript
// XMLHttpRequest를 사용한 HTTP 요청
function fetchUserData(userId, callback) {
  const xhr = new XMLHttpRequest();

  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4) {
      if (xhr.status === 200) {
        const userData = JSON.parse(xhr.responseText);
        callback(null, userData); // 성공
      } else {
        callback(new Error(`HTTP Error: ${xhr.status}`)); // 실패
      }
    }
  };

  xhr.open('GET', `/api/users/${userId}`);
  xhr.send();
}

// 사용
fetchUserData(123, (error, user) => {
  if (error) {
    console.error('사용자 데이터 가져오기 실패:', error);
    return;
  }
  console.log('사용자 정보:', user);
});
```

### Callback Hell (콜백 지옥)

여러 비동기 작업을 순차적으로 실행해야 할 때 Callback의 한계가 드러난다:

```javascript
// 사용자 정보 → 게시글 목록 → 첫 번째 게시글 → 댓글 목록
fetchUserData(123, (userErr, user) => {
  if (userErr) {
    console.error('사용자 조회 실패:', userErr);
    return;
  }

  fetchUserPosts(user.id, (postsErr, posts) => {
    if (postsErr) {
      console.error('게시글 조회 실패:', postsErr);
      return;
    }

    if (posts.length === 0) {
      console.log('게시글이 없습니다');
      return;
    }

    fetchPostComments(posts[0].id, (commentsErr, comments) => {
      if (commentsErr) {
        console.error('댓글 조회 실패:', commentsErr);
        return;
      }

      console.log('첫 번째 게시글의 댓글:', comments);

      // 더 깊은 중첩이 필요하다면...
      processComments(comments, (processErr, result) => {
        if (processErr) {
          console.error('댓글 처리 실패:', processErr);
          return;
        }

        console.log('최종 결과:', result);
        // 계속해서 중첩이 깊어짐...
      });
    });
  });
});
```

**Callback Hell의 문제점:**

1. **가독성 저하**: 코드가 오른쪽으로 계속 깊어짐
2. **에러 처리 복잡**: 각 단계마다 에러 처리 코드 중복
3. **유지보수 어려움**: 로직 수정 시 전체 구조 파악 필요
4. **디버깅 곤란**: 스택 트레이스 추적 어려움

<br></br>

## Promise의 등장과 혁신

### Promise란?

Promise는 "**미래에 완료될 작업의 결과를 나타내는 객체**"다. ES6(ES2015)에서 공식 표준으로 도입되었다.

```javascript
// Promise의 기본 구조
const promise = new Promise((resolve, reject) => {
  // 비동기 작업 수행
  setTimeout(() => {
    const success = Math.random() > 0.5;

    if (success) {
      resolve('작업 성공!'); // 성공 시 resolve 호출
    } else {
      reject(new Error('작업 실패!')); // 실패 시 reject 호출
    }
  }, 1000);
});

// Promise 사용
promise
  .then((result) => {
    console.log('성공:', result);
  })
  .catch((error) => {
    console.error('실패:', error);
  });
```

### Promise의 상태 (States)

Promise는 3가지 상태를 가진다:

| 상태          | 설명                          | 변경 가능 여부        |
| ------------- | ----------------------------- | --------------------- |
| **Pending**   | 초기 상태, 아직 완료되지 않음 | 다른 상태로 변경 가능 |
| **Fulfilled** | 작업이 성공적으로 완료됨      | 불변 (Immutable)      |
| **Rejected**  | 작업이 실패함                 | 불변 (Immutable)      |

```javascript
// Promise 상태 변화 예시
const promise = new Promise((resolve, reject) => {
  console.log('Promise 상태: Pending');

  setTimeout(() => {
    const random = Math.random();

    if (random > 0.5) {
      resolve(`성공! (${random})`);
      // 상태: Pending → Fulfilled
    } else {
      reject(new Error(`실패! (${random})`));
      // 상태: Pending → Rejected
    }
  }, 1000);
});

promise
  .then((value) => {
    console.log('Fulfilled 상태:', value);
  })
  .catch((error) => {
    console.log('Rejected 상태:', error.message);
  });
```

### Promise Chaining (연쇄 호출)

Promise의 가장 큰 장점은 **체이닝**(**Chaining**)을 통한 순차적 비동기 처리다:

```javascript
// 기본 체이닝 예시
function fetchUser(id) {
  return new Promise((resolve) => {
    setTimeout(() => resolve({ id, name: '홍길동' }), 100);
  });
}

function fetchPosts(userId) {
  return new Promise((resolve) => {
    setTimeout(() => resolve([{ id: 1, title: '게시글 1' }]), 100);
  });
}

// Promise 체이닝으로 순차 처리
fetchUser(123)
  .then((user) => {
    console.log('사용자:', user.name);
    return fetchPosts(user.id); // 다음 Promise 반환
  })
  .then((posts) => {
    console.log('게시글 수:', posts.length);
    return posts[0].title; // 값 변환 후 전달
  })
  .then((firstTitle) => {
    console.log('첫 번째 제목:', firstTitle);
  })
  .catch((error) => {
    console.error('에러:', error);
  });

// 출력:
// 사용자: 홍길동
// 게시글 수: 1
// 첫 번째 제목: 게시글 1
```

**체이닝의 핵심 규칙:**

- `.then()`은 항상 새로운 Promise를 반환
- 반환값이 Promise가 아니면 자동으로 resolved Promise로 감싸짐
- 하나의 `.catch()`로 모든 단계의 에러를 처리 가능

<br></br>

## 핵심 차이점 비교

### 1. 코드 구조와 가독성

#### Callback 방식

```javascript
// 중첩된 구조 (Callback Hell)
getData(param, (err, data) => {
  if (err) return handleError(err);

  processData(data, (err, processed) => {
    if (err) return handleError(err);

    saveData(processed, (err, result) => {
      if (err) return handleError(err);
      console.log('완료:', result);
    });
  });
});
```

#### Promise 방식

```javascript
// 평면적 구조 (체이닝)
getData(param)
  .then((data) => processData(data))
  .then((processed) => saveData(processed))
  .then((result) => console.log('완료:', result))
  .catch(handleError);
```

### 2. 에러 처리

#### Callback의 에러 처리

```javascript
// 각 단계마다 에러 확인 필요
step1((err, result1) => {
  if (err) return callback(err);

  step2(result1, (err, result2) => {
    if (err) return callback(err);

    step3(result2, (err, result3) => {
      if (err) return callback(err);
      callback(null, result3);
    });
  });
});
```

#### Promise의 에러 처리

```javascript
// 하나의 .catch()로 모든 에러 처리
step1()
  .then((result1) => step2(result1))
  .then((result2) => step3(result2))
  .then((result3) => console.log(result3))
  .catch((error) => console.error(error)); // 통합 에러 처리
```

### 3. 병렬 처리

#### Callback으로 병렬 처리

```javascript
// 복잡한 상태 관리 필요
let completed = 0;
let results = [];

fetchData(1, (err, data1) => {
  if (err) return handleError(err);
  results[0] = data1;
  if (++completed === 3) processResults(results);
});

fetchData(2, (err, data2) => {
  if (err) return handleError(err);
  results[1] = data2;
  if (++completed === 3) processResults(results);
});

fetchData(3, (err, data3) => {
  if (err) return handleError(err);
  results[2] = data3;
  if (++completed === 3) processResults(results);
});
```

#### Promise로 병렬 처리

```javascript
// 간단한 Promise.all 사용
const tasks = [fetchData(1), fetchData(2), fetchData(3)];

Promise.all(tasks)
  .then((results) => processResults(results))
  .catch(handleError);
```

### 4. 값 변환과 체이닝

#### Callback에서의 값 변환

```javascript
// 중간 값을 전달하기 위해 복잡한 중첩 필요
fetchUser(id, (err, user) => {
  if (err) return callback(err);

  const userName = user.name.toUpperCase(); // 변환

  fetchPosts(user.id, (err, posts) => {
    if (err) return callback(err);

    callback(null, { userName, postCount: posts.length });
  });
});
```

#### Promise에서의 값 변환

```javascript
// .then()에서 자유롭게 값 변환 가능
fetchUser(id)
  .then((user) => ({
    userName: user.name.toUpperCase(), // 변환
    userId: user.id,
  }))
  .then((data) => fetchPosts(data.userId).then((posts) => ({ ...data, postCount: posts.length })))
  .then((result) => console.log(result));
```

<br></br>

## Promise의 고급 기능

### Promise.all() - 모든 작업 완료 대기

```javascript
// 여러 사용자 데이터를 동시에 가져오기
const userPromises = [fetchUserData(1), fetchUserData(2), fetchUserData(3)];

Promise.all(userPromises)
  .then((users) => {
    console.log('모든 사용자 데이터:', users);
    // 모든 Promise가 성공해야 실행됨
  })
  .catch((error) => {
    console.error('하나라도 실패하면 실행:', error);
    // 하나라도 실패하면 즉시 실행됨
  });
```

### Promise.race() - 가장 먼저 완료되는 작업

```javascript
// 여러 서버 중 가장 빠른 응답 사용
const serverRequests = [
  fetch('https://api1.example.com/data'),
  fetch('https://api2.example.com/data'),
  fetch('https://api3.example.com/data'),
];

Promise.race(serverRequests)
  .then((response) => {
    console.log('가장 빠른 서버의 응답:', response);
    return response.json();
  })
  .then((data) => {
    console.log('데이터:', data);
  })
  .catch((error) => {
    console.error('모든 서버 요청 실패:', error);
  });
```

### Promise.allSettled() - 모든 작업 결과 확인

```javascript
// 실패한 작업이 있어도 모든 결과를 확인
const mixedPromises = [
  Promise.resolve('성공 1'),
  Promise.reject(new Error('실패 1')),
  Promise.resolve('성공 2'),
  Promise.reject(new Error('실패 2')),
];

Promise.allSettled(mixedPromises).then((results) => {
  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      console.log(`Promise ${index + 1} 성공:`, result.value);
    } else {
      console.log(`Promise ${index + 1} 실패:`, result.reason.message);
    }
  });
});

// 출력:
// Promise 1 성공: 성공 1
// Promise 2 실패: 실패 1
// Promise 3 성공: 성공 2
// Promise 4 실패: 실패 2
```

### Promise.any() - 하나라도 성공하면 완료

```javascript
// 여러 백업 서버 중 하나라도 성공하면 OK
const backupServers = [
  fetch('https://backup1.example.com/data'),
  fetch('https://backup2.example.com/data'),
  fetch('https://backup3.example.com/data'),
];

Promise.any(backupServers)
  .then((response) => {
    console.log('백업 서버 중 하나 성공:', response);
    return response.json();
  })
  .catch((error) => {
    console.error('모든 백업 서버 실패:', error);
    // AggregateError: 모든 Promise가 실패했을 때 발생
  });
```

<br></br>

## 실제 개발에서의 활용

### Fetch API와 Promise

```javascript
// 현대적인 HTTP 요청 방식
async function getUserProfile(userId) {
  try {
    // 사용자 기본 정보
    const userResponse = await fetch(`/api/users/${userId}`);
    if (!userResponse.ok) {
      throw new Error(`사용자 조회 실패: ${userResponse.status}`);
    }
    const user = await userResponse.json();

    // 사용자의 게시글과 팔로워 정보를 병렬로 가져오기
    const [postsResponse, followersResponse] = await Promise.all([
      fetch(`/api/users/${userId}/posts`),
      fetch(`/api/users/${userId}/followers`),
    ]);

    const posts = await postsResponse.json();
    const followers = await followersResponse.json();

    return {
      user: user,
      posts: posts,
      followers: followers,
      summary: {
        postCount: posts.length,
        followerCount: followers.length,
      },
    };
  } catch (error) {
    console.error('프로필 조회 실패:', error);
    throw error;
  }
}

// 사용
getUserProfile(123)
  .then((profile) => {
    console.log('사용자 프로필:', profile);
    updateUI(profile);
  })
  .catch((error) => {
    showErrorMessage('프로필을 불러올 수 없습니다.');
  });
```

### 타임아웃과 재시도 로직

```javascript
// Promise를 사용한 타임아웃과 재시도
function fetchWithTimeout(url, timeout = 5000) {
  return Promise.race([
    fetch(url),
    new Promise((_, reject) => {
      setTimeout(() => reject(new Error('요청 타임아웃')), timeout);
    }),
  ]);
}

function fetchWithRetry(url, maxRetries = 3) {
  return new Promise((resolve, reject) => {
    let attempts = 0;

    function attempt() {
      attempts++;

      fetchWithTimeout(url)
        .then(resolve)
        .catch((error) => {
          if (attempts >= maxRetries) {
            reject(new Error(`${maxRetries}번 시도 후 실패: ${error.message}`));
          } else {
            console.log(`시도 ${attempts} 실패, 재시도 중...`);
            setTimeout(attempt, 1000 * attempts); // 지수 백오프
          }
        });
    }

    attempt();
  });
}

// 사용
fetchWithRetry('/api/important-data', 3)
  .then((response) => response.json())
  .then((data) => console.log('데이터 수신:', data))
  .catch((error) => console.error('최종 실패:', error));
```

### Promise를 사용한 캐싱

```javascript
// Promise 기반 캐시 시스템
class PromiseCache {
  constructor() {
    this.cache = new Map();
  }

  get(key, fetcher) {
    if (this.cache.has(key)) {
      return this.cache.get(key);
    }

    const promise = Promise.resolve(fetcher()).catch((error) => {
      // 실패한 Promise는 캐시에서 제거
      this.cache.delete(key);
      throw error;
    });

    this.cache.set(key, promise);
    return promise;
  }

  clear(key) {
    if (key) {
      this.cache.delete(key);
    } else {
      this.cache.clear();
    }
  }
}

// 사용
const cache = new PromiseCache();

function getCachedUserData(userId) {
  return cache.get(`user_${userId}`, () => {
    console.log(`실제 API 호출: 사용자 ${userId}`);
    return fetch(`/api/users/${userId}`).then((r) => r.json());
  });
}

// 첫 번째 호출 - API 요청 발생
getCachedUserData(123).then((user) => console.log('첫 번째:', user));

// 두 번째 호출 - 캐시된 Promise 반환
getCachedUserData(123).then((user) => console.log('두 번째:', user));
```

<br></br>

## 자주 묻는 질문 (FAQ)

### Q: Promise를 사용하면 Callback보다 항상 좋나요?

**아니요.** 특수한 상황에서는 Callback이 더 적합할 수 있습니다:

#### 1. 극한 성능이 중요한 경우

```javascript
// 게임 엔진의 렌더링 루프 (60fps 유지 필요)
function gameLoop(callback) {
  requestAnimationFrame(() => {
    // 매 프레임마다 Promise 객체 생성 오버헤드 회피
    updateGame();
    renderGame();
    callback(); // 직접 콜백 호출
  });
}
```

#### 2. 이벤트 리스너 패턴

```javascript
// DOM 이벤트는 여러 번 발생하므로 콜백 방식이 자연스러움
button.addEventListener('click', handleClick); // Promise보다 적합
emitter.on('data', processData); // Node.js EventEmitter
```

<details>
<summary>DOM 이벤트에서 콜백 방식이 자연스러운 이유</summary>

**1. Promise는 "일회성" 결과를 다루기 위해 설계됨**

```javascript
// Promise는 한 번만 resolve/reject 됨
const promise = new Promise((resolve) => {
  resolve('완료!'); // 첫 번째 호출만 유효
  resolve('무시됨'); // 이후 호출은 무시됨
});

promise.then((result) => console.log(result)); // '완료!'만 출력
```

**2. DOM 이벤트는 "반복적" 발생**

```javascript
// 클릭할 때마다 계속 발생해야 함
button.addEventListener('click', () => {
  console.log('클릭됨!'); // 매번 실행되어야 함
});

// Promise로 구현하면 비효율적
button.addEventListener('click', () => {
  Promise.resolve('클릭됨!').then(console.log); // 매번 새 Promise 객체 생성
});
```

**3. 이벤트의 본질적 특성**

```javascript
// 이벤트는 "구독-발행" 패턴
// - 여러 리스너가 동일한 이벤트를 구독 가능
// - 이벤트는 언제든 발생할 수 있음
// - 이벤트는 연속적으로 발생 가능

button.addEventListener('click', handler1);
button.addEventListener('click', handler2); // 동일 이벤트에 여러 핸들러
button.addEventListener('click', handler3);

// 한 번 클릭하면 모든 핸들러가 실행됨
```

**4. Promise로 이벤트를 처리하려면 복잡해짐**

```javascript
// Promise로 이벤트 처리 시도 (비효율적)
function waitForClick() {
  return new Promise((resolve) => {
    button.addEventListener('click', resolve, { once: true }); // 한 번만 처리
  });
}

// 계속 클릭을 기다리려면 반복해야 함
async function handleClicks() {
  while (true) {
    await waitForClick(); // 매번 새 Promise + 새 이벤트 리스너
    console.log('클릭됨!');
  }
}
```

**5. 메모리와 성능 측면**

```javascript
// 콜백 방식: 이벤트 리스너 하나만 등록
button.addEventListener('click', handleClick); // 메모리 효율적

// Promise 방식: 매번 객체 생성
button.addEventListener('click', () => {
  Promise.resolve().then(() => {
    // 매 클릭마다 Promise 객체 + then 콜백 생성
    console.log('클릭됨!');
  });
});
```

**6. 이벤트 취소와 관리**

```javascript
// 콜백 방식: 쉬운 제거
const handler = () => console.log('클릭');
button.addEventListener('click', handler);
button.removeEventListener('click', handler); // 간단한 제거

// Promise 방식: 복잡한 관리
let clickPromises = [];
function addClickHandler() {
  const promise = new Promise((resolve) => {
    const handler = () => resolve();
    button.addEventListener('click', handler);
    // 나중에 제거하려면 handler 참조를 어딘가 저장해야 함
  });
  clickPromises.push(promise);
}
```

**결론:** DOM 이벤트는 **연속적이고 반복적인 신호**이므로, **일회성 비동기 작업**을 위한 Promise보다는 **구독-발행 패턴**의 콜백이 더 자연스럽고 효율적입니다.

</details>

#### 3. 스트리밍 데이터 처리

```javascript
// 실시간 데이터 스트림 (계속해서 데이터가 들어옴)
const stream = new ReadableStream();
stream.on('data', (chunk) => {
  processChunk(chunk); // 각 청크마다 Promise 생성은 비효율
});
```

#### 4. 기존 라이브러리와의 호환성

```javascript
// 오래된 라이브러리나 Node.js 코어 API
const fs = require('fs');
fs.readFile('file.txt', callback); // 이미 콜백 기반으로 설계됨
```

#### 5. 마이크로 최적화가 필요한 라이브러리

```javascript
// 수백만 번 호출되는 유틸리티 함수
function fastOperation(data, callback) {
  // Promise 객체 생성 비용 절약
  const result = heavyComputation(data);
  callback(result);
}
```

하지만 이런 경우들도 대부분 **성능과 개발 편의성의 트레이드오프**를 고려해서 결정해야 합니다.

**일반적인 비동기 작업에서는 Promise를 권장하는 이유:**

1. **가독성**: 코드가 훨씬 읽기 쉬움
2. **유지보수성**: 로직 변경이 용이함
3. **에러 처리**: 통합된 에러 처리 가능
4. **조합성**: 여러 비동기 작업을 쉽게 조합
5. **표준화**: ES6+ 표준으로 널리 지원

### Q: Promise가 오버헤드를 가지는 이유는 무엇인가요?

Promise가 약간의 오버헤드를 가지는 주요 이유들:

#### 1. 객체 생성 비용

```javascript
// 매번 새로운 Promise 객체 생성
new Promise((resolve, reject) => {
  // 메모리 할당 필요
});
```

#### 2. 마이크로태스크 큐 처리

```javascript
// 동기 실행
const result = syncFunction();

// 비동기 실행 (마이크로태스크 큐에 추가)
Promise.resolve(value).then(callback);
```

#### 3. 콜백 체인 관리

```javascript
// Promise는 내부적으로 콜백 체인을 관리
promise
  .then(handler1) // 각 then마다 새로운 Promise 생성
  .then(handler2) // 체인 관리 오버헤드
  .catch(errorHandler);
```

#### 4. 상태 추적

Promise는 pending → fulfilled/rejected 상태 변화를 추적해야 하므로 추가 메모리와 처리 시간이 필요합니다.

하지만 이런 오버헤드는 대부분의 실제 애플리케이션에서는 무시할 수 있을 정도로 작습니다.

### Q: async/await과 Promise의 관계는?

**async/await**는 Promise를 더 쉽게 사용할 수 있게 해주는 **문법적 설탕(Syntactic Sugar)**이다:

```javascript
// Promise 체이닝 방식
function getUserData(userId) {
  return fetchUser(userId)
    .then((user) => {
      console.log('사용자:', user);
      return fetchUserPosts(user.id);
    })
    .then((posts) => {
      console.log('게시글:', posts);
      return posts.length;
    })
    .catch((error) => {
      console.error('에러:', error);
      throw error;
    });
}

// async/await 방식 (동일한 동작)
async function getUserData(userId) {
  try {
    const user = await fetchUser(userId);
    console.log('사용자:', user);

    const posts = await fetchUserPosts(user.id);
    console.log('게시글:', posts);

    return posts.length;
  } catch (error) {
    console.error('에러:', error);
    throw error;
  }
}

// 두 방식 모두 Promise를 반환
console.log(getUserData(123) instanceof Promise); // true
```

### Q: Callback을 Promise로 변환하려면?

**Promisify** 패턴을 사용한다:

```javascript
// 기존 Callback 기반 함수
function oldAsyncFunction(param, callback) {
  setTimeout(() => {
    if (param > 0) {
      callback(null, `결과: ${param}`);
    } else {
      callback(new Error('유효하지 않은 매개변수'));
    }
  }, 1000);
}

// Promise로 변환
function promisifiedFunction(param) {
  return new Promise((resolve, reject) => {
    oldAsyncFunction(param, (error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    });
  });
}

// 또는 유틸리티 함수 사용
function promisify(fn) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      fn(...args, (error, result) => {
        if (error) {
          reject(error);
        } else {
          resolve(result);
        }
      });
    });
  };
}

// 사용
const promisifiedOld = promisify(oldAsyncFunction);
promisifiedOld(5)
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```

### Q: Promise 체인에서 중간에 값을 전달하려면?

여러 방법이 있다:

```javascript
// 방법 1: 객체로 데이터 축적
function processUserWorkflow(userId) {
  return fetchUser(userId)
    .then((user) => {
      return fetchUserPosts(user.id).then((posts) => ({ user, posts })); // 객체로 묶어서 전달
    })
    .then(({ user, posts }) => {
      return fetchPostComments(posts[0].id).then((comments) => ({ user, posts, comments }));
    })
    .then(({ user, posts, comments }) => {
      return {
        userName: user.name,
        postCount: posts.length,
        commentCount: comments.length,
      };
    });
}

// 방법 2: async/await으로 더 간단하게
async function processUserWorkflow(userId) {
  const user = await fetchUser(userId);
  const posts = await fetchUserPosts(user.id);
  const comments = await fetchPostComments(posts[0].id);

  return {
    userName: user.name,
    postCount: posts.length,
    commentCount: comments.length,
  };
}

// 방법 3: Promise.all로 병렬 처리
async function processUserWorkflow(userId) {
  const user = await fetchUser(userId);

  // 게시글과 다른 정보를 병렬로 가져오기
  const [posts, profile] = await Promise.all([fetchUserPosts(user.id), fetchUserProfile(user.id)]);

  return {
    userName: user.name,
    postCount: posts.length,
    profileInfo: profile,
  };
}
```

### Q: Promise에서 발생하는 Unhandled Rejection은 무엇인가요?

**Unhandled Promise Rejection**은 Promise가 rejected 되었지만 `.catch()`로 처리되지 않은 경우 발생한다:

```javascript
// 잘못된 예시 - Unhandled Rejection 발생
function problematicFunction() {
  return Promise.reject(new Error('의도적 에러'));
}

problematicFunction(); // .catch() 없음 - Unhandled Rejection!

// 브라우저 콘솔/Node.js에서 경고 메시지 출력:
// "Uncaught (in promise) Error: 의도적 에러"

// 올바른 처리
problematicFunction().catch((error) => {
  console.error('에러 처리됨:', error.message);
});

// 또는 async/await 사용
async function handleAsync() {
  try {
    await problematicFunction();
  } catch (error) {
    console.error('에러 처리됨:', error.message);
  }
}

// 전역 에러 핸들러 설정 (최후의 수단)
window.addEventListener('unhandledrejection', (event) => {
  console.error('처리되지 않은 Promise 거부:', event.reason);
  event.preventDefault(); // 브라우저의 기본 에러 표시 방지
});

// Node.js에서는
process.on('unhandledRejection', (reason, promise) => {
  console.error('처리되지 않은 Promise 거부:', reason);
});
```
