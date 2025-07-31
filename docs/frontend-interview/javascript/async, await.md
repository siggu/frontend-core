---
sidebar_position: 2
---

# async, await

async/await는 Promise를 기반으로 한 비동기 처리의 문법적 설탕(Syntactic Sugar)으로, 비동기 코드를 마치 동기 코드처럼 직관적이고 읽기 쉽게 작성할 수 있게 해준다. ES2017(ES8)에서 도입된 이 기능은 Promise의 복잡한 체이닝을 단순화하고, 에러 처리를 더욱 명확하게 만들어준다.

## async/await란?

async/await는 Promise 기반의 비동기 처리를 위한 **문법적 설탕**이다. 내부적으로는 여전히 Promise를 사용하지만, 코드를 더욱 직관적으로 작성할 수 있게 해준다.

### async와 await의 역할

**async 함수**는 항상 Promise를 반환하는 함수를 정의한다:

```javascript
// async 함수 정의
async function fetchData() {
  return '데이터'; // 자동으로 Promise.resolve('데이터')가 됨
}

// 일반 함수와 비교
function normalFetch() {
  return Promise.resolve('데이터');
}

// 두 함수는 동일한 결과
console.log(fetchData() instanceof Promise); // true
console.log(normalFetch() instanceof Promise); // true
```

**await 키워드**는 Promise가 resolve될 때까지 함수의 실행을 일시 정지시킨다:

```javascript
async function example() {
  console.log('1. 시작');

  const result = await fetchData(); // Promise가 resolve될 때까지 대기
  console.log('3. 결과:', result);

  return '완료';
}

console.log('2. async 함수 호출 후');
example();

// 출력 순서:
// 1. 시작
// 2. async 함수 호출 후
// 3. 결과: 데이터
```

<br></br>

## async/await의 기본 문법

### async 함수 선언

```javascript
// 함수 선언문
async function fetchUser(id) {
  // 비동기 작업 수행
}

// 함수 표현식
const fetchUser = async function (id) {
  // 비동기 작업 수행
};

// 화살표 함수
const fetchUser = async (id) => {
  // 비동기 작업 수행
};

// 메서드 정의
const userService = {
  async fetchUser(id) {
    // 비동기 작업 수행
  },
};

// 클래스 메서드
class UserService {
  async fetchUser(id) {
    // 비동기 작업 수행
  }
}
```

### await 사용 규칙

**1. await는 async 함수 내부에서만 사용 가능**

```javascript
// ❌ 잘못된 사용 - async 함수 외부에서 await 사용
function wrongUsage() {
  const result = await fetchData(); // SyntaxError!
}

// ✅ 올바른 사용 - async 함수 내부에서 await 사용
async function correctUsage() {
  const result = await fetchData(); // 정상 동작
}
```

**2. await는 Promise나 thenable 객체에만 사용**

```javascript
async function example() {
  // Promise에 await 사용
  const data1 = await fetch('/api/data');

  // 일반 값에 await 사용 (의미 없지만 에러는 아님)
  const data2 = await '일반 문자열'; // Promise.resolve('일반 문자열')와 동일

  // Promise를 반환하는 함수에 await 사용
  const data3 = await someAsyncFunction();
}
```

<br></br>

## Promise vs async/await 비교

### 1. 코드 구조와 가독성

**Promise 체이닝 방식:**

```javascript
// Promise 체이닝
function getUserProfile(userId) {
  return fetchUser(userId)
    .then((user) => {
      console.log('사용자:', user.name);
      return fetchUserPosts(user.id);
    })
    .then((posts) => {
      console.log('게시글 수:', posts.length);
      return fetchPostComments(posts[0].id);
    })
    .then((comments) => {
      console.log('댓글 수:', comments.length);
      return {
        user: user, // ❌ 접근 불가 - 스코프 밖
        posts: posts, // ❌ 접근 불가 - 스코프 밖
        comments: comments,
      };
    })
    .catch((error) => {
      console.error('에러 발생:', error);
      throw error;
    });
}
```

**async/await 방식:**

```javascript
// async/await
async function getUserProfile(userId) {
  try {
    const user = await fetchUser(userId);
    console.log('사용자:', user.name);

    const posts = await fetchUserPosts(user.id);
    console.log('게시글 수:', posts.length);

    const comments = await fetchPostComments(posts[0].id);
    console.log('댓글 수:', comments.length);

    return {
      user: user, // ✅ 접근 가능
      posts: posts, // ✅ 접근 가능
      comments: comments,
    };
  } catch (error) {
    console.error('에러 발생:', error);
    throw error;
  }
}
```

### 2. 에러 처리

**Promise의 에러 처리:**

```javascript
// Promise - 복잡한 에러 처리
function processData() {
  return step1()
    .then((result1) => {
      return step2(result1);
    })
    .then((result2) => {
      return step3(result2);
    })
    .catch((error) => {
      if (error.message.includes('step1')) {
        console.error('1단계 에러:', error);
      } else if (error.message.includes('step2')) {
        console.error('2단계 에러:', error);
      } else {
        console.error('3단계 에러:', error);
      }
      throw error;
    });
}
```

**async/await의 에러 처리:**

```javascript
// async/await - 직관적인 에러 처리
async function processData() {
  try {
    const result1 = await step1();
    const result2 = await step2(result1);
    const result3 = await step3(result2);
    return result3;
  } catch (error) {
    // 모든 단계의 에러를 한 곳에서 처리
    console.error('처리 중 에러 발생:', error);
    throw error;
  }
}

// 개별 단계별 에러 처리도 가능
async function processDataWithDetailedError() {
  let result1, result2, result3;

  try {
    result1 = await step1();
  } catch (error) {
    console.error('1단계 실패:', error);
    throw error;
  }

  try {
    result2 = await step2(result1);
  } catch (error) {
    console.error('2단계 실패:', error);
    throw error;
  }

  try {
    result3 = await step3(result2);
  } catch (error) {
    console.error('3단계 실패:', error);
    throw error;
  }

  return result3;
}
```

### 3. 병렬 처리

**Promise의 병렬 처리:**

```javascript
// Promise.all을 사용한 병렬 처리
function fetchAllData() {
  const tasks = [fetchUser(1), fetchUser(2), fetchUser(3)];

  return Promise.all(tasks).then((users) => {
    console.log('모든 사용자:', users);
    return users;
  });
}
```

**async/await의 병렬 처리:**

```javascript
// async/await로 병렬 처리
async function fetchAllData() {
  // 방법 1: Promise.all 사용
  const users = await Promise.all([fetchUser(1), fetchUser(2), fetchUser(3)]);
  console.log('모든 사용자:', users);
  return users;
}

// 방법 2: Promise를 먼저 생성한 후 await
async function fetchAllDataAlternative() {
  // Promise들을 먼저 시작 (병렬 실행)
  const userPromise1 = fetchUser(1);
  const userPromise2 = fetchUser(2);
  const userPromise3 = fetchUser(3);

  // 모든 Promise를 await (결과 대기)
  const user1 = await userPromise1;
  const user2 = await userPromise2;
  const user3 = await userPromise3;

  return [user1, user2, user3];
}

// ❌ 잘못된 순차 처리 (병렬이 아님)
async function fetchAllDataSequential() {
  const user1 = await fetchUser(1); // 1번 완료 후
  const user2 = await fetchUser(2); // 2번 완료 후
  const user3 = await fetchUser(3); // 3번 완료

  return [user1, user2, user3]; // 총 시간 = 각각의 시간 합
}
```

<br></br>

## 실제 개발에서의 활용

### HTTP 요청 처리

```javascript
// Fetch API와 async/await
async function getUserData(userId) {
  try {
    // 사용자 기본 정보 가져오기
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

    // 응답 검증
    if (!postsResponse.ok) {
      throw new Error(`게시글 조회 실패: ${postsResponse.status}`);
    }
    if (!followersResponse.ok) {
      throw new Error(`팔로워 조회 실패: ${followersResponse.status}`);
    }

    const [posts, followers] = await Promise.all([postsResponse.json(), followersResponse.json()]);

    return {
      user,
      posts,
      followers,
      summary: {
        postCount: posts.length,
        followerCount: followers.length,
      },
    };
  } catch (error) {
    console.error('사용자 데이터 조회 실패:', error);
    throw error;
  }
}

// 사용 예시
async function displayUserProfile(userId) {
  try {
    const profileData = await getUserData(userId);
    updateUI(profileData);
  } catch (error) {
    showErrorMessage('프로필을 불러올 수 없습니다.');
  }
}
```

### 파일 처리 (Node.js)

```javascript
const fs = require('fs').promises;
const path = require('path');

// 여러 파일을 순차적으로 처리
async function processFiles(fileNames) {
  const results = [];

  for (const fileName of fileNames) {
    try {
      console.log(`처리 중: ${fileName}`);

      const filePath = path.join('./data', fileName);
      const content = await fs.readFile(filePath, 'utf8');
      const processedContent = content.toUpperCase();

      const outputPath = path.join('./output', `processed_${fileName}`);
      await fs.writeFile(outputPath, processedContent);

      results.push({
        fileName,
        status: 'success',
        outputPath,
      });
    } catch (error) {
      console.error(`${fileName} 처리 실패:`, error);
      results.push({
        fileName,
        status: 'error',
        error: error.message,
      });
    }
  }

  return results;
}

// 여러 파일을 병렬로 처리
async function processFilesParallel(fileNames) {
  const promises = fileNames.map(async (fileName) => {
    try {
      const filePath = path.join('./data', fileName);
      const content = await fs.readFile(filePath, 'utf8');
      const processedContent = content.toUpperCase();

      const outputPath = path.join('./output', `processed_${fileName}`);
      await fs.writeFile(outputPath, processedContent);

      return { fileName, status: 'success' };
    } catch (error) {
      return { fileName, status: 'error', error: error.message };
    }
  });

  return await Promise.all(promises);
}
```

### 데이터베이스 트랜잭션

```javascript
// 데이터베이스 트랜잭션 처리
async function transferMoney(fromUserId, toUserId, amount) {
  const transaction = await db.beginTransaction();

  try {
    // 1. 보내는 사용자 계좌 확인
    const fromUser = await db.query('SELECT balance FROM users WHERE id = ? FOR UPDATE', [fromUserId], { transaction });

    if (fromUser.balance < amount) {
      throw new Error('잔액이 부족합니다');
    }

    // 2. 받는 사용자 계좌 확인
    const toUser = await db.query('SELECT id FROM users WHERE id = ? FOR UPDATE', [toUserId], { transaction });

    if (!toUser) {
      throw new Error('받는 사용자를 찾을 수 없습니다');
    }

    // 3. 보내는 사용자 계좌에서 차감
    await db.query('UPDATE users SET balance = balance - ? WHERE id = ?', [amount, fromUserId], { transaction });

    // 4. 받는 사용자 계좌에 추가
    await db.query('UPDATE users SET balance = balance + ? WHERE id = ?', [amount, toUserId], { transaction });

    // 5. 거래 내역 기록
    await db.query(
      'INSERT INTO transactions (from_user_id, to_user_id, amount, created_at) VALUES (?, ?, ?, NOW())',
      [fromUserId, toUserId, amount],
      { transaction }
    );

    // 모든 작업이 성공하면 커밋
    await transaction.commit();

    return { success: true, message: '송금이 완료되었습니다' };
  } catch (error) {
    // 에러 발생시 롤백
    await transaction.rollback();
    console.error('송금 실패:', error);
    return { success: false, message: error.message };
  }
}
```

<br></br>

## async/await의 고급 활용

### 반복문과 함께 사용하기

```javascript
// for...of 루프에서 순차 처리
async function processItemsSequentially(items) {
  const results = [];

  for (const item of items) {
    const result = await processItem(item);
    results.push(result);
    console.log(`${item} 처리 완료`);
  }

  return results;
}

// map과 Promise.all로 병렬 처리
async function processItemsParallel(items) {
  const promises = items.map((item) => processItem(item));
  return await Promise.all(promises);
}

// forEach는 await와 함께 사용하면 안됨
async function wrongWay(items) {
  const results = [];

  // ❌ 잘못된 방법 - forEach는 await를 기다리지 않음
  items.forEach(async (item) => {
    const result = await processItem(item);
    results.push(result); // 예상과 다른 결과
  });

  return results; // 빈 배열이 반환될 가능성
}
```

### 조건부 비동기 처리

```javascript
async function conditionalAsyncOperation(condition, data) {
  let result;

  if (condition === 'fast') {
    result = await fastOperation(data);
  } else if (condition === 'slow') {
    result = await slowOperation(data);
  } else {
    result = data; // 동기 처리
  }

  return result;
}

// 옵셔널 체이닝과 함께 사용
async function safeAsyncOperation(user) {
  if (user?.id) {
    const profile = await fetchUserProfile(user.id);
    return profile?.data || null;
  }
  return null;
}
```

### 타임아웃과 재시도 로직

```javascript
// 타임아웃 기능이 있는 비동기 함수
function withTimeout(promise, ms) {
  return Promise.race([
    promise,
    new Promise((_, reject) => {
      setTimeout(() => reject(new Error('타임아웃')), ms);
    }),
  ]);
}

// 재시도 로직이 있는 비동기 함수
async function withRetry(asyncFn, maxRetries = 3, delay = 1000) {
  let lastError;

  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await withTimeout(asyncFn(), 5000);
    } catch (error) {
      lastError = error;
      console.log(`시도 ${attempt} 실패:`, error.message);

      if (attempt < maxRetries) {
        await new Promise((resolve) => setTimeout(resolve, delay * attempt));
      }
    }
  }

  throw new Error(`${maxRetries}번 시도 후 실패: ${lastError.message}`);
}

// 사용 예시
async function fetchDataWithRetry() {
  try {
    const data = await withRetry(() => fetch('/api/unreliable-endpoint'));
    return await data.json();
  } catch (error) {
    console.error('최종 실패:', error);
    throw error;
  }
}
```

<br></br>

## 자주 묻는 질문 (FAQ)

### Q: async/await를 사용하면 Promise보다 항상 좋나요?

**상황에 따라 다릅니다.** 각각의 장단점이 있습니다:

**async/await의 장점:**

- 코드가 동기 코드처럼 읽기 쉬움
- 에러 처리가 try/catch로 통일됨
- 중간 값들에 쉽게 접근 가능
- 디버깅이 더 쉬움

**Promise의 장점:**

- 함수형 프로그래밍 스타일
- 체이닝이 명확할 때 더 간결
- 병렬 처리 로직이 더 명확

```javascript
// Promise가 더 적합한 경우
function simpleChain() {
  return fetchData().then(transform).then(validate).then(save);
}

// async/await가 더 적합한 경우
async function complexLogic() {
  const user = await fetchUser();
  const permissions = await fetchPermissions(user.role);

  if (permissions.includes('admin')) {
    const adminData = await fetchAdminData();
    return { ...user, adminData };
  }

  return user;
}
```

### Q: async 함수에서 동기 코드와 비동기 코드를 섞어 사용해도 되나요?

**네, 문제없습니다.** async 함수 내에서 동기 코드와 비동기 코드를 자유롭게 섞어 사용할 수 있습니다:

```javascript
async function mixedOperations() {
  // 동기 코드
  const startTime = Date.now();
  console.log('작업 시작');

  // 비동기 코드
  const data = await fetchData();

  // 동기 코드
  const processedData = data.map((item) => item.toUpperCase());

  // 비동기 코드
  await saveData(processedData);

  // 동기 코드
  const endTime = Date.now();
  console.log(`작업 완료 (${endTime - startTime}ms)`);

  return processedData;
}
```

### Q: await 없이 async 함수를 호출하면 어떻게 되나요?

**Promise 객체가 반환됩니다.** await 없이 async 함수를 호출하면 함수가 즉시 실행되지만, 결과를 기다리지 않습니다:

```javascript
async function slowOperation() {
  await new Promise((resolve) => setTimeout(resolve, 1000));
  return '완료!';
}

// await 없이 호출
console.log('1. 시작');
const promise = slowOperation(); // Promise 객체 반환
console.log('2. 중간'); // 즉시 실행
console.log(promise); // Promise { <pending> }

// 나중에 결과를 확인하려면
promise.then((result) => console.log('3. 결과:', result));

// 또는 다시 await 사용
async function main() {
  const result = await promise;
  console.log('4. await 결과:', result);
}
```

### Q: async/await에서 병렬 처리를 어떻게 하나요?

**Promise를 먼저 생성한 후 await하거나 Promise.all을 사용합니다:**

```javascript
// ❌ 순차 처리 (느림)
async function sequential() {
  const user = await fetchUser(1); // 1초 대기
  const posts = await fetchPosts(1); // 1초 대기
  const comments = await fetchComments(1); // 1초 대기
  return { user, posts, comments }; // 총 3초
}

// ✅ 병렬 처리 방법 1: Promise 먼저 생성
async function parallel1() {
  const userPromise = fetchUser(1);
  const postsPromise = fetchPosts(1);
  const commentsPromise = fetchComments(1);

  const user = await userPromise; // 최대 1초 대기
  const posts = await postsPromise; // 즉시 완료 (이미 실행 중)
  const comments = await commentsPromise; // 즉시 완료 (이미 실행 중)

  return { user, posts, comments }; // 총 1초
}

// ✅ 병렬 처리 방법 2: Promise.all 사용
async function parallel2() {
  const [user, posts, comments] = await Promise.all([fetchUser(1), fetchPosts(1), fetchComments(1)]);

  return { user, posts, comments }; // 총 1초
}
```

### Q: async 함수에서 일반 값을 return하면 어떻게 되나요?

**자동으로 Promise.resolve()로 감싸집니다:**

```javascript
async function returnString() {
  return '안녕하세요'; // Promise.resolve('안녕하세요')와 동일
}

async function returnNumber() {
  return 42; // Promise.resolve(42)와 동일
}

async function returnObject() {
  return { name: '홍길동' }; // Promise.resolve({ name: '홍길동' })와 동일
}

// 사용
async function test() {
  const str = await returnString(); // '안녕하세요'
  const num = await returnNumber(); // 42
  const obj = await returnObject(); // { name: '홍길동' }

  console.log(str, num, obj);
}

// 또는 .then() 사용
returnString().then((str) => console.log(str)); // '안녕하세요'
```

### Q: try/catch 없이 async/await의 에러를 처리할 수 있나요?

**네, .catch()를 사용할 수 있습니다:**

```javascript
// 방법 1: try/catch 사용
async function withTryCatch() {
  try {
    const result = await riskyOperation();
    return result;
  } catch (error) {
    console.error('에러:', error);
    return null;
  }
}

// 방법 2: .catch() 사용
async function withCatch() {
  const result = await riskyOperation().catch((error) => {
    console.error('에러:', error);
    return null; // 기본값 반환
  });

  return result;
}

// 방법 3: 유틸리티 함수 사용
function to(promise) {
  return promise.then((data) => [null, data]).catch((error) => [error, null]);
}

async function withUtility() {
  const [error, result] = await to(riskyOperation());

  if (error) {
    console.error('에러:', error);
    return null;
  }

  return result;
}
```

### Q: async/await로 무한 루프나 성능 문제가 발생할 수 있나요?

**잘못 사용하면 가능합니다.** 주의해야 할 패턴들:

```javascript
// ❌ 무한 루프 위험
async function infiniteRetry() {
  while (true) {
    try {
      return await unreliableOperation();
    } catch (error) {
      console.log('재시도 중...'); // 무한 재시도
    }
  }
}

// ✅ 개선된 버전
async function safeRetry(maxAttempts = 3) {
  let attempts = 0;

  while (attempts < maxAttempts) {
    try {
      return await unreliableOperation();
    } catch (error) {
      attempts++;
      if (attempts >= maxAttempts) throw error;

      await new Promise((resolve) => setTimeout(resolve, 1000)); // 지연
      console.log(`재시도 ${attempts}/${maxAttempts}`);
    }
  }
}

// ❌ 메모리 누수 위험
async function memoryLeak() {
  const results = [];

  while (true) {
    const data = await fetchLargeData();
    results.push(data); // 계속 누적됨
  }
}

// ✅ 개선된 버전
async function processStream() {
  while (true) {
    const data = await fetchLargeData();
    await processData(data); // 처리 후 메모리 해제

    // 종료 조건 추가
    if (shouldStop()) break;
  }
}
```
