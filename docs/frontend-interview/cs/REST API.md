---
sidebar_position: 5
---

# REST API

REST(Representational State Transfer)는 웹 서비스를 설계하기 위한 아키텍처 스타일이다. HTTP 프로토콜을 기반으로 하여 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에 웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일이다.

## REST란 무엇인가?

### REST의 기본 개념

REST는 2000년 로이 필딩(Roy Fielding)의 박사학위 논문에서 처음 소개된 개념으로, **자원**(**Resource**)을 **URI**로 표현하고, **HTTP Method**를 통해 해당 자원에 대한 **행위**(**Verb**)를 정의하는 방식이다.

**REST의 핵심 구성 요소:**

1. **자원(Resource)**: URI로 식별되는 모든 것
2. **행위(Verb)**: HTTP Method (GET, POST, PUT, DELETE 등)
3. **표현(Representation)**: JSON, XML 등의 데이터 형식

```javascript
// REST의 기본 구조
HTTP Method + URI + Representation = REST API

// 예시
GET /api/users/123 → 사용자 123번 조회
POST /api/users → 새 사용자 생성
PUT /api/users/123 → 사용자 123번 전체 수정
DELETE /api/users/123 → 사용자 123번 삭제
```

### REST의 6가지 제약 조건

#### 1. Client-Server (클라이언트-서버)

클라이언트와 서버가 서로 독립적으로 분리되어 있어야 한다.

```javascript
// 클라이언트 (React 앱)
const fetchUser = async (userId) => {
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
};

// 서버 (Express.js)
app.get('/api/users/:id', (req, res) => {
  const user = getUserById(req.params.id);
  res.json(user);
});
```

#### 2. Stateless (무상태성)

서버는 클라이언트의 상태 정보를 저장하지 않는다.

```javascript
// 잘못된 예시 (상태 저장)
app.get('/api/step1', (req, res) => {
  req.session.step = 1; // 서버에 상태 저장 - REST 위반
  res.json({ message: '1단계 완료' });
});

// 올바른 예시 (무상태)
app.post('/api/orders', (req, res) => {
  const { userId, items, shippingInfo } = req.body; // 모든 정보를 요청에 포함
  const order = createOrder(userId, items, shippingInfo);
  res.json(order);
});
```

#### 3. Cacheable (캐시 가능)

HTTP의 캐싱 기능을 활용할 수 있어야 한다.

```javascript
// 캐시 헤더 설정
app.get('/api/users/:id', (req, res) => {
  const user = getUserById(req.params.id);

  // 캐시 설정 (1시간)
  res.set('Cache-Control', 'public, max-age=3600');
  res.set('ETag', generateETag(user));

  res.json(user);
});
```

#### 4. Uniform Interface (일관된 인터페이스)

URI로 지정한 자원에 대한 조작을 통일되고 한정적인 인터페이스로 수행한다.

```javascript
// 일관된 인터페이스 예시
GET / api / users; // 사용자 목록 조회
GET / api / users / 123; // 특정 사용자 조회
POST / api / users; // 새 사용자 생성
PUT / api / users / 123; // 사용자 전체 수정
PATCH / api / users / 123; // 사용자 부분 수정
DELETE / api / users / 123; // 사용자 삭제
```

#### 5. Layered System (계층화)

클라이언트는 서버에 직접 연결되는지, 중간 서버를 통해 연결되는지 알 수 없어야 한다.

```javascript
// 계층화된 시스템 예시
클라이언트 → Load Balancer → API Gateway → 마이크로서비스

// 클라이언트는 중간 계층을 인식하지 못함
fetch('/api/users/123') // 실제로는 여러 계층을 거침
```

#### 6. Code-On-Demand (주문형 코드) - 선택사항

서버가 클라이언트에 실행 가능한 코드를 전송할 수 있다.

<br></br>

## HTTP Method와 CRUD

### HTTP Method별 특징

| HTTP Method | CRUD   | 멱등성 | 안전성 | 설명             |
| ----------- | ------ | ------ | ------ | ---------------- |
| GET         | Read   | ✅     | ✅     | 리소스 조회      |
| POST        | Create | ❌     | ❌     | 리소스 생성      |
| PUT         | Update | ✅     | ❌     | 리소스 전체 수정 |
| PATCH       | Update | ❌     | ❌     | 리소스 부분 수정 |
| DELETE      | Delete | ✅     | ❌     | 리소스 삭제      |

## URI 설계 원칙

### RESTful URI 설계 규칙

#### 1. 명사를 사용하고 동사를 피한다

```javascript
// 올바른 예시
GET / api / users; // 사용자 목록 조회
POST / api / users; // 사용자 생성
GET / api / users / 123; // 사용자 조회
PUT / api / users / 123; // 사용자 수정
DELETE / api / users / 123; // 사용자 삭제

// 잘못된 예시
GET / api / getUsers; // 동사 사용 - 지양
POST / api / createUser; // 동사 사용 - 지양
GET / api / deleteUser / 123; // 동사 사용 - 지양
```

#### 2. 복수형 명사를 사용한다

```javascript
// 올바른 예시
GET / api / users; // 복수형 사용
GET / api / posts; // 복수형 사용
GET / api / comments; // 복수형 사용

// 잘못된 예시
GET / api / user; // 단수형 - 일관성 없음
GET / api / post; // 단수형 - 일관성 없음
```

#### 3. 계층 관계를 명확히 표현한다

```javascript
// 중첩 리소스 설계
GET / api / users / 123 / posts; // 사용자 123의 게시글 목록
GET / api / users / 123 / posts / 456; // 사용자 123의 게시글 456
GET / api / posts / 456 / comments; // 게시글 456의 댓글 목록
GET / api / posts / 456 / comments / 789; // 게시글 456의 댓글 789

// 실제 구현
app.get('/api/users/:userId/posts', async (req, res) => {
  try {
    const posts = await Post.find({ author: req.params.userId })
      .populate('author', 'name email')
      .sort({ createdAt: -1 });

    res.json(posts);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

#### 4. 필터링, 정렬, 페이징은 쿼리 파라미터를 사용한다

```javascript
// 쿼리 파라미터 활용
GET /api/posts?category=tech&sort=createdAt&order=desc&page=1&limit=10

// 구현 예시
app.get('/api/posts', async (req, res) => {
  try {
    const {
      category,
      sort = 'createdAt',
      order = 'desc',
      page = 1,
      limit = 10,
      search
    } = req.query;

    // 필터 조건 구성
    const filter = {};
    if (category) filter.category = category;
    if (search) {
      filter.$or = [
        { title: { $regex: search, $options: 'i' } },
        { content: { $regex: search, $options: 'i' } }
      ];
    }

    // 정렬 조건 구성
    const sortOrder = order === 'desc' ? -1 : 1;
    const sortObj = { [sort]: sortOrder };

    const posts = await Post.find(filter)
      .sort(sortObj)
      .limit(limit * 1)
      .skip((page - 1) * limit)
      .populate('author', 'name email');

    const total = await Post.countDocuments(filter);

    res.json({
      posts,
      pagination: {
        currentPage: parseInt(page),
        totalPages: Math.ceil(total / limit),
        totalItems: total,
        hasNext: page * limit < total,
        hasPrev: page > 1
      }
    });

  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

<br></br>

## 자주 묻는 질문 (FAQ)

### Q: REST API와 RESTful API의 차이는?

**REST API**: REST 아키텍처 스타일을 따르는 API
**RESTful API**: REST 원칙을 잘 지켜서 설계된 API

```javascript
// REST API (기본적인 REST 원칙만 따름)
GET /api/users
POST /api/users

// RESTful API (REST 원칙을 잘 지킴)
GET /api/users?page=1&limit=10&sort=name
POST /api/users
PUT /api/users/123
PATCH /api/users/123
DELETE /api/users/123

// 응답도 일관성 있게 구조화
{
  "data": {...},
  "message": "성공",
  "status": 200,
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### Q: PUT과 PATCH의 실제 차이는?

**PUT**: 리소스 전체를 교체
**PATCH**: 리소스 일부를 수정

```javascript
// 사용자 데이터
const user = {
  id: 123,
  name: "홍길동",
  email: "hong@example.com",
  bio: "개발자입니다",
  age: 30
};

// PUT 요청 (전체 교체)
PUT /api/users/123
{
  "name": "김철수",
  "email": "kim@example.com"
  // bio, age 누락 → null이나 기본값으로 설정됨
}

// PATCH 요청 (부분 수정)
PATCH /api/users/123
{
  "name": "김철수"
  // email, bio, age는 기존 값 유지
}
```

### Q: API 버전 관리는 어떻게 하나요?

**방법 1: URL 경로에 버전 명시**

```javascript
// v1 API
GET / api / v1 / users;
POST / api / v1 / users;

// v2 API (새로운 기능 추가)
GET / api / v2 / users;
POST / api / v2 / users;
```

**방법 2: 헤더로 버전 관리**

```javascript
// 요청 헤더
Accept: application / vnd.api + json;
version = 1;
Accept: application / vnd.api + json;
version = 2;

// 서버에서 처리
app.get('/api/users', (req, res) => {
  const version = req.headers.accept.includes('version=2') ? 'v2' : 'v1';

  if (version === 'v2') {
    // v2 로직
  } else {
    // v1 로직
  }
});
```

### Q: 에러 처리는 어떻게 일관성 있게 하나요?

**통일된 에러 응답 형식**:

```javascript
// 에러 응답 미들웨어
const errorHandler = (err, req, res, next) => {
  const error = {
    message: err.message || '서버 오류가 발생했습니다',
    status: err.status || 500,
    code: err.code || 'INTERNAL_SERVER_ERROR',
    timestamp: new Date().toISOString(),
    path: req.originalUrl,
  };

  // 개발 환경에서만 스택 트레이스 포함
  if (process.env.NODE_ENV === 'development') {
    error.stack = err.stack;
  }

  res.status(error.status).json({ error });
};

// 사용 예시
app.get('/api/users/:id', async (req, res, next) => {
  try {
    const user = await User.findById(req.params.id);
    if (!user) {
      const error = new Error('사용자를 찾을 수 없습니다');
      error.status = 404;
      error.code = 'USER_NOT_FOUND';
      throw error;
    }
    res.json(user);
  } catch (error) {
    next(error); // 에러 핸들러로 전달
  }
});
```

### Q: API 보안은 어떻게 처리하나요?

**기본적인 보안 조치**:

```javascript
// 1. 인증 미들웨어
const authenticate = (req, res, next) => {
  const token = req.header('Authorization')?.replace('Bearer ', '');

  if (!token) {
    return res.status(401).json({
      error: '인증 토큰이 필요합니다',
    });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({
      error: '유효하지 않은 토큰입니다',
    });
  }
};

// 2. 권한 확인 미들웨어
const authorize = (roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({
        error: '권한이 없습니다',
      });
    }
    next();
  };
};

// 3. 사용 예시
app.delete('/api/users/:id', authenticate, authorize(['admin', 'moderator']), async (req, res) => {
  // 관리자 또는 운영자만 접근 가능
});

// 4. Rate Limiting
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15분
  max: 100, // 최대 100회 요청
  message: '너무 많은 요청이 발생했습니다. 잠시 후 다시 시도해주세요.',
});

app.use('/api', limiter);
```
