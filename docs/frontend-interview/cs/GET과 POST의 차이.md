---
sidebar_position: 2
---

# GET과 POST의 차이점

HTTP(HyperText Transfer Protocol)는 웹에서 클라이언트와 서버 간의 통신을 위한 프로토콜이다. 그 중에서도 GET과 POST는 가장 핵심적인 메서드로, 웹 개발에서 반드시 이해해야 할 개념이다.

<!-- ![HTTP 메서드 구조](./svg/HTTP%20메서드%20구조.svg) -->

## HTTP 메서드란?

HTTP 메서드는 클라이언트가 서버에게 "**무엇을 하고 싶은지**"를 알려주는 방법이다. 마치 우리가 일상에서 "가져와줘", "만들어줘", "수정해줘", "삭제해줘"라고 말하는 것처럼, HTTP에서도 각각의 의도를 표현하는 메서드가 있다.

### HTTP 메서드의 분류

HTTP 메서드는 크게 두 가지 특성으로 분류된다:

1. **안전성(Safe)**: 서버의 상태를 변경하지 않는지 여부
2. **멱등성(Idempotent)**: 동일한 요청을 여러 번 해도 결과가 같은지 여부

| 메서드 | 안전성 | 멱등성 | 용도             |
| ------ | ------ | ------ | ---------------- |
| GET    | ✅     | ✅     | 데이터 조회      |
| POST   | ❌     | ❌     | 데이터 생성      |
| PUT    | ❌     | ✅     | 데이터 전체 수정 |
| PATCH  | ❌     | ❌     | 데이터 부분 수정 |
| DELETE | ❌     | ✅     | 데이터 삭제      |

<br></br>

## GET과 POST의 본질적 차이

### GET 메서드의 철학

GET은 **"안전한(Safe)"** 메서드로 설계되었다. 이는 서버의 상태를 절대 변경하지 않는다는 의미다.

**GET의 핵심 원칙:**

- **읽기 전용**: 데이터를 가져오기만 하고 변경하지 않음
- **멱등성**: 몇 번을 호출해도 같은 결과
- **캐시 가능**: 같은 요청의 결과를 저장해두고 재사용 가능
- **북마크 가능**: URL에 모든 정보가 포함되어 있어 북마크로 저장 가능

### POST 메서드의 철학

POST는 **"안전하지 않은(Unsafe)"** 메서드로, 서버의 상태를 변경할 수 있다.

**POST의 핵심 원칙:**

- **쓰기 작업**: 새로운 데이터를 생성하거나 기존 데이터를 변경
- **비멱등성**: 호출할 때마다 새로운 결과나 부작용 발생
- **캐시 불가**: 기본적으로 캐싱하지 않음
- **보안성**: 중요한 데이터를 URL에 노출하지 않음

<br></br>

## 데이터 전송 방식의 차이

### GET 요청의 데이터 전송

GET 요청에서는 모든 데이터가 **URL의 쿼리 스트링**에 포함된다.

```
https://api.example.com/search?q=javascript&category=frontend&page=1&limit=10
```

**쿼리 스트링 구조:**

- `?`: 쿼리 스트링의 시작을 나타냄
- `key=value`: 매개변수와 값의 쌍
- `&`: 여러 매개변수를 구분하는 구분자

**GET 요청 예시:**

```http
GET /api/users?page=1&limit=10&sort=name HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 (Chrome/91.0)
Accept: application/json
```

### POST 요청의 데이터 전송

POST 요청에서는 데이터가 **HTTP 요청 본문**(**Request Body**)에 포함된다.

**POST 요청 예시:**

```http
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/json
Content-Length: 87
User-Agent: Mozilla/5.0 (Chrome/91.0)

{
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 30,
  "department": "개발팀"
}
```

**POST 데이터 형식들:**

1. **application/json** (가장 일반적)

```http
Content-Type: application/json

{"name": "홍길동", "age": 30}
```

2. **application/x-www-form-urlencoded** (HTML 폼 기본값)

```http
Content-Type: application/x-www-form-urlencoded

name=%ED%99%8D%EA%B8%B8%EB%8F%99&age=30
```

3. **multipart/form-data** (파일 업로드)

```http
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="name"

홍길동
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="profile.jpg"
Content-Type: image/jpeg

[바이너리 데이터]
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

<br></br>

## 주요 특징 비교

### 1. 데이터 크기 제한

#### GET의 크기 제한

GET 요청은 URL 길이 제한이 있다:

| 브라우저/서버     | URL 최대 길이    |
| ----------------- | ---------------- |
| Internet Explorer | 2,083자          |
| Chrome            | 8,182자          |
| Firefox           | 65,536자         |
| Safari            | 80,000자         |
| Apache 서버       | 8,190자 (기본값) |
| Nginx 서버        | 4,096자 (기본값) |

```javascript
// GET 요청 - 너무 긴 URL은 문제가 될 수 있음
const longQuery = 'a'.repeat(10000);
fetch(`/api/search?q=${longQuery}`); // 브라우저/서버에 따라 실패할 수 있음
```

#### POST의 크기 제한

POST 요청은 이론적으로 제한이 없지만, 실제로는 서버 설정에 따라 다르다:

```javascript
// Apache 설정 예시
// LimitRequestBody 100000000  # 100MB 제한

// Express.js 설정 예시
app.use(express.json({ limit: '50mb' }));
app.use(express.urlencoded({ limit: '50mb', extended: true }));
```

### 2. 보안과 데이터 노출

#### GET의 보안 취약점

1. **URL 노출**

```
# 브라우저 주소창에 모든 데이터가 노출됨
https://example.com/login?username=admin&password=1234
```

2. **서버 로그에 기록**

```
# 웹 서버 액세스 로그 예시
192.168.1.100 - - [25/Dec/2023:10:00:00 +0000] "GET /login?username=admin&password=1234 HTTP/1.1" 200 1234
```

3. **브라우저 히스토리**

```javascript
// 브라우저 히스토리에서 확인 가능
history.back(); // 이전 페이지로 돌아가면 URL의 모든 데이터 확인 가능
```

4. **리퍼러 헤더**

```http
# 다른 사이트로 이동할 때 현재 URL이 Referer 헤더로 전송됨
GET /external-site HTTP/1.1
Referer: https://mysite.com/search?secret=confidential_data
```

#### POST의 보안 장점

```http
POST /api/login HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "username": "admin",
  "password": "1234"
}
```

- URL에 민감한 정보가 노출되지 않음
- 브라우저 히스토리에 데이터가 남지 않음
- 리퍼러 헤더에 데이터가 포함되지 않음

:::warning 중요한 보안 사실

<!-- **HTTPS 없이는 GET과 POST 모두 안전하지 않습니다!** -->

HTTP 통신에서는 둘 다 평문으로 전송되어 네트워크 스니핑으로 쉽게 확인할 수 있다.
:::

### 3. 멱등성(Idempotency)

#### GET의 멱등성

```javascript
// 같은 GET 요청을 여러 번 해도 서버 상태는 변하지 않음
fetch('/api/users/1'); // 사용자 정보 조회
fetch('/api/users/1'); // 동일한 결과, 서버 상태 변화 없다
fetch('/api/users/1'); // 동일한 결과, 서버 상태 변화 없다
```

#### POST의 비멱등성

```javascript
// 같은 POST 요청을 여러 번 하면 매번 새로운 리소스가 생성된다
fetch('/api/users', {
  method: 'POST',
  body: JSON.stringify({ name: '홍길동' }),
});
// 첫 번째 요청: ID 1인 사용자 생성
// 두 번째 요청: ID 2인 사용자 생성 (중복 생성 문제!)
```

**POST 중복 요청 방지 방법:**

1. **토큰 기반 방식**

```javascript
// 서버에서 고유 토큰을 발급한다
const token = await fetch('/api/tokens').then((r) => r.json());

// 요청 시 토큰을 포함한다
fetch('/api/users', {
  method: 'POST',
  headers: { 'X-Idempotency-Key': token.key },
  body: JSON.stringify({ name: '홍길동' }),
});
```

2. **클라이언트 사이드 방식**

```javascript
let isSubmitting = false;

function submitForm() {
  if (isSubmitting) return; // 중복 요청을 방지한다

  isSubmitting = true;
  fetch('/api/users', { method: 'POST', ... })
    .finally(() => { isSubmitting = false; });
}
```

### 4. 캐싱

#### GET의 캐싱

브라우저는 GET 요청을 자동으로 캐싱한다:

```http
# 첫 번째 요청
GET /api/users?page=1 HTTP/1.1

# 응답 헤더
HTTP/1.1 200 OK
Cache-Control: max-age=3600
ETag: "abc123"

# 두 번째 요청(1시간 이내) - 캐시된 결과 사용
# 네트워크 요청 없이 브라우저 캐시에서 바로 응답
```

**캐시 제어 방법:**

```javascript
// 캐시를 방지한다
fetch('/api/users?page=1&_t=' + Date.now()); // 타임스탬프를 추가한다

// 또는 헤더로 제어한다
fetch('/api/users', {
  headers: { 'Cache-Control': 'no-cache' },
});
```

#### POST의 캐싱

POST는 기본적으로 캐싱되지 않지만, 명시적으로 설정 가능하다:

```http
POST /api/users HTTP/1.1
Cache-Control: max-age=300  # 5분간 캐시한다

# 하지만 일반적으로 권장되지 않는다
```

### 5. 브라우저 동작 차이

#### 페이지 새로고침 시 동작

```javascript
// GET 요청 페이지
location.href = '/search?q=javascript';
// F5 새로고침 → 동일한 GET 요청 재실행 (문제없다)

// POST 요청 페이지
// F5 새로고침 → "페이지를 다시 제출하시겠습니까?" 경고창을 표시한다
```

#### 북마크 동작

```javascript
// GET - 북마크 가능
https://shop.com/products?category=electronics&price=100-500

// POST - 북마크 불가능 (URL에 검색 조건이 없다)
https://shop.com/search  # POST body에 검색 조건이 포함된다
```

<br></br>

## 실제 사용 사례와 예시

### 1. 웹 애플리케이션에서의 사용

#### GET 사용 사례

**1. 데이터 조회**

```javascript
// 사용자 목록 조회
const getUsers = async (page = 1, limit = 10) => {
  const response = await fetch(`/api/users?page=${page}&limit=${limit}&sort=createdAt:desc`);
  return response.json();
};

// 특정 사용자 조회
const getUser = async (userId) => {
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
};
```

**2. 검색 기능**

```javascript
// 검색 - URL에 모든 조건이 포함되어 북마크/공유가 가능하다
const searchProducts = async (query, filters) => {
  const params = new URLSearchParams({
    q: query,
    category: filters.category,
    minPrice: filters.minPrice,
    maxPrice: filters.maxPrice,
    inStock: filters.inStock,
  });

  const response = await fetch(`/api/products/search?${params}`);
  return response.json();
};

// 결과 URL: /api/products/search?q=laptop&category=electronics&minPrice=500&maxPrice=2000&inStock=true
```

**3. 필터링 및 정렬**

```javascript
// 게시글 목록 (필터링, 정렬, 페이징)
const getPosts = async (filters) => {
  const params = new URLSearchParams({
    tag: filters.tag,
    author: filters.author,
    sort: filters.sort || 'latest',
    page: filters.page || 1,
  });

  return fetch(`/api/posts?${params}`).then((r) => r.json());
};
```

#### POST 사용 사례

**1. 데이터 생성**

```javascript
// 새 사용자 등록
const createUser = async (userData) => {
  const response = await fetch('/api/users', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      name: userData.name,
      email: userData.email,
      password: userData.password,
      profile: userData.profile,
    }),
  });

  if (!response.ok) {
    throw new Error('사용자 생성 실패');
  }

  return response.json();
};
```

**2. 인증 (로그인)**

```javascript
// 로그인 - 민감한 정보이므로 POST를 사용한다
const login = async (credentials) => {
  const response = await fetch('/api/auth/login', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      email: credentials.email,
      password: credentials.password,
    }),
  });

  if (!response.ok) {
    throw new Error('로그인 실패');
  }

  const { token, user } = await response.json();
  localStorage.setItem('authToken', token);
  return user;
};
```

**3. 파일 업로드**

```javascript
// 파일 업로드 - multipart/form-data를 사용한다
const uploadFile = async (file, metadata) => {
  const formData = new FormData();
  formData.append('file', file);
  formData.append('title', metadata.title);
  formData.append('description', metadata.description);

  const response = await fetch('/api/files/upload', {
    method: 'POST',
    body: formData, // Content-Type이 자동으로 설정된다
  });

  if (!response.ok) {
    throw new Error('파일 업로드 실패');
  }

  return response.json();
};
```

**4. 복잡한 데이터 전송**

```javascript
// 복잡한 주문 데이터 - GET으로는 URL이 너무 길어짐
const createOrder = async (orderData) => {
  const response = await fetch('/api/orders', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${authToken}`,
    },
    body: JSON.stringify({
      items: orderData.items, // 많은 상품 정보
      shippingAddress: orderData.shippingAddress,
      billingAddress: orderData.billingAddress,
      paymentMethod: orderData.paymentMethod,
      couponCodes: orderData.couponCodes,
      specialInstructions: orderData.specialInstructions,
    }),
  });

  return response.json();
};
```

### 2. HTML Form에서의 사용

#### GET Form 예시

```html
<!-- 검색 폼 - 결과를 북마크하거나 공유할 수 있음 -->
<form method="GET" action="/search">
  <input type="text" name="q" placeholder="검색어 입력" />
  <select name="category">
    <option value="all">전체</option>
    <option value="books">도서</option>
    <option value="electronics">전자제품</option>
  </select>
  <input type="number" name="minPrice" placeholder="최저가격" />
  <input type="number" name="maxPrice" placeholder="최고가격" />
  <button type="submit">검색</button>
</form>

<!-- 제출 시 URL: /search?q=노트북&category=electronics&minPrice=500&maxPrice=2000 -->
```

#### POST Form 예시

```html
<!-- 회원가입 폼 - 민감한 정보이므로 POST 사용 -->
<form method="POST" action="/api/users/register">
  <input type="text" name="name" placeholder="이름" required />
  <input type="email" name="email" placeholder="이메일" required />
  <input type="password" name="password" placeholder="비밀번호" required />
  <textarea name="bio" placeholder="자기소개"></textarea>
  <button type="submit">가입하기</button>
</form>

<!-- 파일 업로드 폼 -->
<form method="POST" action="/api/files/upload" enctype="multipart/form-data">
  <input type="file" name="avatar" accept="image/*" />
  <input type="text" name="title" placeholder="파일 제목" />
  <button type="submit">업로드</button>
</form>
```

### 3. RESTful API 설계

#### 완전한 CRUD 예시

```javascript
// 블로그 게시글 API

// 1. 목록 조회 (READ)
GET /api/posts?page=1&limit=10&category=tech&sort=latest
// 응답: { posts: [...], totalCount: 150, currentPage: 1 }

// 2. 특정 게시글 조회 (READ)
GET /api/posts/123
// 응답: { id: 123, title: "...", content: "...", author: {...} }

// 3. 새 게시글 작성 (CREATE)
POST /api/posts
// 요청 본문: { title: "새 글", content: "내용", category: "tech" }
// 응답: { id: 124, title: "새 글", createdAt: "..." }

// 4. 게시글 전체 수정 (UPDATE)
PUT /api/posts/123
// 요청 본문: { title: "수정된 제목", content: "수정된 내용", category: "tech" }

// 5. 게시글 부분 수정 (UPDATE)
PATCH /api/posts/123
// 요청 본문: { title: "제목만 수정" }

// 6. 게시글 삭제 (DELETE)
DELETE /api/posts/123
```

#### 중첩 리소스 예시

```javascript
// 게시글의 댓글 관리

// 특정 게시글의 댓글 목록 조회
GET /api/posts/123/comments?page=1&limit=20

// 새 댓글 작성
POST /api/posts/123/comments
요청 본문: { content: "좋은 글이네요!", parentId: null }

// 댓글 수정
PATCH /api/posts/123/comments/456
// 요청 본문: { content: "수정된 댓글 내용" }

// 댓글 삭제
DELETE /api/posts/123/comments/456
```

### 4. 성능과 최적화 고려사항

#### GET 요청 최적화

```javascript
// 1. 캐싱 활용
const cachedFetch = async (url) => {
  const cached = localStorage.getItem(url);
  if (cached) {
    const { data, timestamp } = JSON.parse(cached);
    if (Date.now() - timestamp < 300000) {
      // 5분 캐시
      return data;
    }
  }

  const response = await fetch(url);
  const data = await response.json();

  localStorage.setItem(
    url,
    JSON.stringify({
      data,
      timestamp: Date.now(),
    })
  );

  return data;
};

// 2. 조건부 요청 (ETag 활용)
const conditionalFetch = async (url, etag) => {
  const headers = {};
  if (etag) {
    headers['If-None-Match'] = etag;
  }

  const response = await fetch(url, { headers });

  if (response.status === 304) {
    // 변경사항 없음, 캐시된 데이터 사용
    return getCachedData(url);
  }

  return response.json();
};
```

#### POST 요청 최적화

```javascript
// 1. 요청 압축
const compressedPost = async (url, data) => {
  const compressed = await compress(JSON.stringify(data)); // gzip 등

  return fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Content-Encoding': 'gzip',
    },
    body: compressed,
  });
};

// 2. 배치 처리
const batchCreate = async (items) => {
  // 여러 개의 POST 요청 대신 한 번에 처리
  return fetch('/api/users/batch', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ users: items }),
  });
};
```

<br></br>

<br></br>

## 심화 주제와 실전 팁

### 멱등성 키(Idempotency Key) 구현

POST 요청의 비멱등성 문제를 해결하는 실전 방법:

```javascript
// 클라이언트 측 구현
class IdempotentRequest {
  constructor() {
    this.pendingRequests = new Map();
  }

  async post(url, data, options = {}) {
    // 멱등성 키 생성 (UUID 또는 요청 내용 기반 해시)
    const idempotencyKey = options.idempotencyKey || this.generateKey(url, data);

    // 이미 진행 중인 동일한 요청이 있다면 기다림
    if (this.pendingRequests.has(idempotencyKey)) {
      return this.pendingRequests.get(idempotencyKey);
    }

    const requestPromise = fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Idempotency-Key': idempotencyKey,
        ...options.headers,
      },
      body: JSON.stringify(data),
    });

    this.pendingRequests.set(idempotencyKey, requestPromise);

    try {
      const result = await requestPromise;
      return result;
    } finally {
      this.pendingRequests.delete(idempotencyKey);
    }
  }

  generateKey(url, data) {
    return btoa(url + JSON.stringify(data)).replace(/[^a-zA-Z0-9]/g, '');
  }
}

// 사용 예시
const client = new IdempotentRequest();

// 동일한 요청을 여러 번 해도 한 번만 실행됨
client.post('/api/orders', orderData);
client.post('/api/orders', orderData); // 첫 번째 요청 결과를 기다림
```

### 서버 측 멱등성 키 처리

```javascript
// Express.js 예시
const processedRequests = new Map();

app.post('/api/orders', async (req, res) => {
  const idempotencyKey = req.headers['idempotency-key'];

  if (idempotencyKey && processedRequests.has(idempotencyKey)) {
    // 이미 처리된 요청이면 기존 결과 반환
    return res.json(processedRequests.get(idempotencyKey));
  }

  try {
    const result = await createOrder(req.body);

    if (idempotencyKey) {
      processedRequests.set(idempotencyKey, result);
      // 일정 시간 후 메모리에서 제거
      setTimeout(() => {
        processedRequests.delete(idempotencyKey);
      }, 24 * 60 * 60 * 1000); // 24시간
    }

    res.json(result);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});
```

### URL 인코딩 심화

#### GET 요청의 특수 문자 처리

```javascript
// 올바른 URL 인코딩
const searchQuery = '검색어 & 특수문자 = 처리';
const encodedQuery = encodeURIComponent(searchQuery);
// 결과: %EA%B2%80%EC%83%89%EC%96%B4%20%26%20%ED%8A%B9%EC%88%98%EB%AC%B8%EC%9E%90%20%3D%20%EC%B2%98%EB%A6%AC

const url = `/api/search?q=${encodedQuery}`;

// URLSearchParams 사용 (자동 인코딩)
const params = new URLSearchParams({
  q: '검색어 & 특수문자 = 처리',
  category: 'books & magazines',
  sort: 'price:asc',
});
const url2 = `/api/search?${params.toString()}`;
```

#### POST 요청의 Content-Type별 인코딩

```javascript
// 1. application/json (자동 인코딩)
fetch('/api/users', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: '홍길동', email: 'test@example.com' }),
});

// 2. application/x-www-form-urlencoded (수동 인코딩 필요)
const formData = new URLSearchParams({
  name: '홍길동',
  email: 'test@example.com',
});
fetch('/api/users', {
  method: 'POST',
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
  body: formData.toString(),
});

// 3. multipart/form-data (브라우저가 자동 처리)
const formData2 = new FormData();
formData2.append('name', '홍길동');
formData2.append('file', fileInput.files[0]);
fetch('/api/users', {
  method: 'POST',
  // body: formData2, // Content-Type 헤더를 설정하지 않음!
});
```

### 에러 처리 및 재시도 로직

```javascript
class RobustHttpClient {
  async request(url, options = {}) {
    const maxRetries = options.maxRetries || 3;
    const retryDelay = options.retryDelay || 1000;

    for (let attempt = 1; attempt <= maxRetries; attempt++) {
      try {
        const response = await fetch(url, options);

        if (response.ok) {
          return response;
        }

        // 4xx 에러는 재시도하지 않음 (클라이언트 오류)
        if (response.status >= 400 && response.status < 500) {
          throw new Error(`Client error: ${response.status}`);
        }

        // 5xx 에러는 재시도 (서버 오류)
        if (attempt === maxRetries) {
          throw new Error(`Server error after ${maxRetries} attempts`);
        }
      } catch (error) {
        if (attempt === maxRetries) {
          throw error;
        }

        // 지수 백오프로 재시도 간격 증가
        await this.delay(retryDelay * Math.pow(2, attempt - 1));
      }
    }
  }

  delay(ms) {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }

  async get(url, options = {}) {
    return this.request(url, { ...options, method: 'GET' });
  }

  async post(url, data, options = {}) {
    return this.request(url, {
      ...options,
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        ...options.headers,
      },
      body: JSON.stringify(data),
    });
  }
}

// 사용 예시
const client = new RobustHttpClient();

try {
  const response = await client.post('/api/users', userData, {
    maxRetries: 5,
    retryDelay: 2000,
  });
  const result = await response.json();
} catch (error) {
  console.error('요청 실패:', error.message);
}
```

<br></br>

## 자주 묻는 질문 (FAQ)

### Q: GET으로도 데이터를 생성할 수 있지 않나요?

**기술적으로는 가능하지만 강력히 권장하지 않는다.** HTTP 명세와 RESTful 원칙에 위배되며, 다음과 같은 문제가 발생할 수 있다:

```javascript
// 잘못된 예시 - GET으로 데이터 생성
app.get('/api/users/create', (req, res) => {
  const { name, email } = req.query;
  // 사용자 생성 로직...
});

// 문제점들:
// 1. 검색엔진 크롤러가 URL을 방문하여 의도치 않은 데이터 생성
// 2. 브라우저 프리페치로 인한 의도치 않은 실행
// 3. URL 공유 시 다른 사람이 실수로 데이터 생성
// 4. 브라우저 히스토리에 민감한 정보 노출
```

**올바른 방법:**

```javascript
// 올바른 예시 - POST로 데이터 생성
app.post('/api/users', (req, res) => {
  const { name, email } = req.body;
  // 사용자 생성 로직...
});
```

### Q: POST 요청도 캐싱할 수 있나요?

**기술적으로 가능하지만 일반적으로 권장하지 않는다.** POST는 서버 상태를 변경하는 작업이므로 캐싱하면 예상치 못한 부작용이 발생할 수 있다.

```javascript
// POST 캐싱이 문제가 되는 경우
fetch('/api/orders', {
  method: 'POST',
  body: JSON.stringify(orderData),
  headers: { 'Cache-Control': 'max-age=300' }, // 5분 캐시
});

// 문제: 같은 주문이 여러 번 생성될 수 있음
```

**예외적으로 캐싱이 유용한 POST 요청:**

```javascript
// 검증이나 계산 결과를 위한 POST (서버 상태 변경 없음)
fetch('/api/validate-form', {
  method: 'POST',
  body: JSON.stringify(formData),
  headers: { 'Cache-Control': 'max-age=60' }, // 1분 캐시
});
```

### Q: 언제 PUT과 PATCH를 사용하나요?

**PUT**은 리소스 전체를 교체할 때, **PATCH**는 일부만 수정할 때 사용한다:

```javascript
// 현재 사용자 데이터
const currentUser = {
  id: 1,
  name: '홍길동',
  email: 'hong@example.com',
  age: 30,
  department: '개발팀',
};

// PUT - 전체 교체 (누락된 필드는 제거되거나 기본값으로 설정)
fetch('/api/users/1', {
  method: 'PUT',
  body: JSON.stringify({
    name: '김철수',
    email: 'kim@example.com',
    age: 25,
    // department 누락 - 제거되거나 null로 설정됨
  }),
});

// PATCH - 부분 수정 (지정된 필드만 변경)
fetch('/api/users/1', {
  method: 'PATCH',
  body: JSON.stringify({
    age: 31, // age만 변경, 다른 필드는 그대로 유지
  }),
});
```

### Q: 파일 업로드는 왜 항상 POST를 사용하나요?

**여러 기술적 제약과 보안상의 이유** 때문이다:

1. **URL 길이 제한**: GET은 URL에 데이터를 포함하므로 파일 크기에 제한이 있다
2. **바이너리 데이터**: URL은 텍스트 기반이므로 바이너리 데이터 전송이 비효율적이다
3. **보안**: 파일 경로나 내용이 URL에 노출되면 보안 위험이 있다

```javascript
// 파일 업로드 - multipart/form-data 사용
const formData = new FormData();
formData.append('file', fileInput.files[0]);
formData.append('description', '프로필 사진');

fetch('/api/upload', {
  method: 'POST',
  body: formData, // Content-Type: multipart/form-data 자동 설정
});
```

### Q: 검색 기능은 왜 GET을 사용하나요?

**사용자 경험과 웹의 기본 원리** 때문이다:

```javascript
// GET 검색의 장점들

// 1. 북마크 가능
const searchUrl = '/search?q=JavaScript&category=tutorial&sort=latest';
// 사용자가 이 URL을 북마크하여 나중에 다시 방문 가능

// 2. 공유 가능
// 검색 결과 URL을 다른 사람과 공유 가능

// 3. 브라우저 히스토리 지원
// 뒤로가기/앞으로가기 버튼이 자연스럽게 동작

// 4. 캐싱 가능
// 같은 검색어에 대한 결과를 캐싱하여 성능 향상

// 5. SEO 친화적
// 검색엔진이 검색 결과 페이지를 인덱싱 가능
```

**POST 검색이 필요한 경우:**

```javascript
// 복잡한 검색 조건이나 민감한 정보가 포함된 경우
fetch('/api/advanced-search', {
  method: 'POST',
  body: JSON.stringify({
    keywords: ['javascript', 'react'],
    dateRange: { start: '2023-01-01', end: '2023-12-31' },
    categories: ['tutorial', 'reference', 'example'],
    userPreferences: { excludeAdult: true },
    personalizedFilters: userProfile.preferences,
  }),
});
```

### Q: HTTPS 없이 POST가 GET보다 안전한가요?

**아니다.** HTTP에서는 GET과 POST 모두 평문으로 전송되어 동일하게 취약하다:

```javascript
// HTTP에서의 네트워크 패킷 (둘 다 평문으로 전송됨)

// GET 요청
// GET /login?username=admin&password=1234 HTTP/1.1
// Host: example.com

// POST 요청
// POST /login HTTP/1.1
// Host: example.com
// Content-Type: application/json
//
// {"username":"admin","password":"1234"}

둘 다 네트워크 스니핑으로 쉽게 확인 가능!
```

**진정한 보안은 HTTPS에서만 가능하다:**

```javascript
// HTTPS에서는 모든 데이터가 암호화됨
// TLS/SSL로 암호화되어 네트워크 스니핑으로도 내용 확인 불가

// 하지만 HTTPS에서도 GET은 여전히 위험:
// - 서버 로그에 URL 전체가 기록됨
// - 브라우저 히스토리에 민감한 정보 저장됨
// - Referer 헤더로 다른 사이트에 정보 누출 가능
```
