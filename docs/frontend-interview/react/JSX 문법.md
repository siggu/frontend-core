---
sidebar_position: 5
---

# JSX 문법

## JSX 탄생의 철학적 배경

### 분리된 기술들의 통합 철학

**전통적인 관심사 분리의 한계**
웹 개발에서 전통적으로 HTML(구조), CSS(스타일), JavaScript(동작)를 분리하는 것이 모범 사례로 여겨졌습니다. 하지만 React 팀은 이것이 진정한 "관심사의 분리"가 아니라 단순한 "기술의 분리"라고 보았습니다.

**컴포넌트 기반 관심사 분리**
React는 "기술별 분리" 대신 "기능별 분리"를 제안했습니다. 하나의 UI 컴포넌트와 관련된 모든 것(구조, 스타일, 로직)을 한 곳에 모으는 것이 더 자연스럽다고 본 것입니다.

### JavaScript와 마크업의 융합 철학

**선언적 UI의 구현**
JSX는 "UI가 특정 시점에 어떻게 보여야 하는가"를 선언적으로 표현할 수 있게 해줍니다. 이는 명령형 DOM 조작과는 근본적으로 다른 접근법입니다.

**JavaScript의 표현력과 HTML의 직관성 결합**
JSX는 JavaScript의 모든 표현력(변수, 함수, 조건문, 반복문 등)을 유지하면서도 HTML의 직관적인 구조를 제공합니다.

## JSX가 해결하려 한 근본적 문제

### 템플릿 언어의 한계 극복

**기존 템플릿 시스템의 문제점**
Handlebars, Mustache, Angular 템플릿 등은 제한적인 로직만 표현할 수 있었습니다. 복잡한 조건부 렌더링이나 데이터 변환은 별도의 헬퍼 함수나 컨트롤러에서 처리해야 했습니다.

**JavaScript의 완전한 표현력 활용**
JSX는 템플릿이 아니라 JavaScript 표현식이므로, JavaScript의 모든 기능을 제약 없이 사용할 수 있습니다.

### 문자열 기반 템플릿의 취약점

**타입 안정성의 부재**
전통적인 문자열 기반 템플릿은 컴파일 타임에 오류를 발견하기 어려웠습니다. 변수명 오타나 속성 이름 실수가 런타임에서야 발견되곤 했습니다.

**도구 지원의 한계**
문자열 안의 HTML은 에디터의 신택스 하이라이팅, 자동완성, 리팩토링 도구의 지원을 받기 어려웠습니다.

**JSX의 해결책**
JSX는 JavaScript AST(추상 구문 트리)의 일부이므로, 정적 분석 도구들이 완벽하게 지원할 수 있습니다.

## JSX의 변환 원리와 그 의미

### 트랜스파일레이션의 철학

**컴파일 타임 최적화**
JSX는 런타임이 아닌 컴파일 타임에 처리됩니다. 이는 런타임 성능에 영향을 주지 않으면서도 개발자 경험을 향상시킵니다.

**추상화 계층의 투명성**
JSX는 `React.createElement()` 호출로 변환되는 단순한 문법 설탕입니다. 이 변환 과정이 투명하고 예측 가능하다는 것이 중요합니다.

### Virtual DOM과의 관계

**JSX는 Virtual DOM의 선언적 표현**
JSX로 작성된 컴포넌트는 Virtual DOM 객체를 생성하는 함수로 변환됩니다. 이는 React의 핵심 철학인 "UI = f(state)"를 직접적으로 구현한 것입니다.

**함수형 프로그래밍과의 조화**
JSX 표현식은 본질적으로 함수 호출이므로, 함수형 프로그래밍의 합성(composition) 원칙과 자연스럽게 조화됩니다.

## JSX의 설계 원칙과 철학

### 최소 놀라움의 원칙 (Principle of Least Surprise)

**HTML과의 유사성**
JSX는 의도적으로 HTML과 비슷하게 설계되었습니다. 기존 웹 개발자들이 쉽게 적응할 수 있도록 하기 위함입니다.

**JavaScript의 일관성**
동시에 JSX는 JavaScript의 일관성을 해치지 않으려 노력했습니다. camelCase 속성명, JavaScript 표현식 사용 등이 그 예입니다.

### 명시성과 간결성의 균형

**명시적 import의 철학**
React 17 이전까지는 JSX를 사용하는 파일에서 반드시 `import React from 'react'`를 해야 했습니다. 이는 JSX가 React에 의존한다는 것을 명시적으로 보여주기 위함이었습니다.

**새로운 JSX Transform의 의미**
React 17부터 도입된 새로운 JSX Transform은 자동으로 필요한 함수를 import합니다. 이는 개발자 경험을 향상시키면서도 번들 크기를 최적화합니다.

### 확장성과 호환성

**커스텀 컴포넌트와 HTML 요소의 통일된 문법**
JSX에서는 HTML 요소와 React 컴포넌트를 동일한 문법으로 사용할 수 있습니다. 이는 컴포넌트 기반 아키텍처의 철학을 반영합니다.

**Props의 일관된 전달 방식**
HTML 속성과 React props가 동일한 방식으로 전달되어, 개발자가 일관된 멘탈 모델을 가질 수 있습니다.

## JSX와 다른 접근법들의 철학적 차이

### Template Literals vs JSX

**Template Literals의 한계**
ES6의 template literals는 문자열 기반이므로 타입 체킹이나 정적 분석이 어렵습니다.

**JSX의 구조적 접근**
JSX는 구조화된 데이터(Virtual DOM 객체)를 생성하므로, 런타임 검증과 최적화가 가능합니다.

### HTML-in-JS vs JS-in-HTML

**Vue.js의 템플릿 접근법**
Vue.js는 HTML에 JavaScript를 삽입하는 방식을 택했습니다. 이는 디자이너와의 협업에 유리할 수 있지만, JavaScript의 표현력에 제약이 있습니다.

**React의 JSX 철학**
React는 JavaScript 안에 HTML을 포함하는 방식을 선택했습니다. 이는 프로그래머 중심적 접근이지만, 더 강력한 추상화를 가능하게 합니다.

## JSX가 가져온 패러다임의 변화

### 컴포넌트 중심 사고의 강화

**재사용 가능한 UI 블록**
JSX는 UI를 재사용 가능한 함수로 만들 수 있게 해주었습니다. 이는 UI 개발을 "페이지 만들기"에서 "컴포넌트 조립하기"로 바꿨습니다.

**합성을 통한 복잡성 관리**
작은 컴포넌트들을 조합하여 복잡한 UI를 만드는 패턴이 JSX를 통해 자연스럽게 표현됩니다.

### 선언적 UI 프로그래밍의 대중화

**상태 기반 UI 설계**
JSX는 "현재 상태에서 UI가 어떻게 보여야 하는가"를 선언적으로 표현하게 만들었습니다.

**조건부 렌더링의 자연스러운 표현**
JavaScript의 삼항 연산자, 논리 연산자 등을 통해 조건부 렌더링을 자연스럽게 표현할 수 있게 되었습니다.

## JSX의 철학이 다른 기술에 미친 영향

### 다른 프레임워크들의 변화

**Vue.js의 JSX 지원**
Vue.js도 템플릿 외에 JSX를 지원하기 시작했습니다.

**Svelte의 컴파일 타임 접근**
Svelte는 JSX와 다른 방식이지만, 컴파일 타임 최적화라는 아이디어를 공유합니다.

### 모바일과 데스크톱 개발로의 확산

**React Native의 JSX**
JSX의 철학이 모바일 개발(React Native)로 확장되었습니다.

**Flutter의 선언적 UI**
Flutter의 위젯 시스템도 JSX와 유사한 선언적 UI 철학을 보여줍니다.

## JSX란 무엇인가?

**JSX(JavaScript XML)**는 React에서 사용하는 JavaScript의 문법 확장입니다. HTML과 비슷한 문법으로 JavaScript 안에서 UI를 기술할 수 있게 해주는 문법 설탕(syntactic sugar)입니다.

## JSX의 기본 개념

### JSX vs HTML

```jsx
// JSX
const element = <h1>Hello, World!</h1>;

// 일반 HTML
<h1>Hello, World!</h1>;
```

JSX는 HTML과 매우 비슷하지만, 실제로는 JavaScript 코드입니다. Babel과 같은 트랜스파일러가 JSX를 일반 JavaScript로 변환해줍니다.

### JSX 변환 과정

```jsx
// JSX 코드
const element = <h1 className='greeting'>Hello, World!</h1>;

// 변환된 JavaScript (React 17 이전)
const element = React.createElement('h1', { className: 'greeting' }, 'Hello, World!');

// 변환된 JavaScript (React 17+)
import { jsx as _jsx } from 'react/jsx-runtime';
const element = _jsx('h1', {
  className: 'greeting',
  children: 'Hello, World!',
});
```

## JSX 문법 규칙

### 1. 단일 부모 요소

JSX는 반드시 하나의 부모 요소로 감싸져야 합니다.

```jsx
// 잘못된 예
function App() {
  return (
    <h1>제목</h1>
    <p>내용</p>  // 에러!
  );
}

// 올바른 예 1: div로 감싸기
function App() {
  return (
    <div>
      <h1>제목</h1>
      <p>내용</p>
    </div>
  );
}

// 올바른 예 2: React.Fragment 사용
function App() {
  return (
    <React.Fragment>
      <h1>제목</h1>
      <p>내용</p>
    </React.Fragment>
  );
}

// 올바른 예 3: 빈 태그 사용 (Fragment 단축 문법)
function App() {
  return (
    <>
      <h1>제목</h1>
      <p>내용</p>
    </>
  );
}
```

### 2. JavaScript 표현식 사용

중괄호 `{}` 안에 JavaScript 표현식을 넣을 수 있습니다.

```jsx
function Greeting({ name, age }) {
  const isAdult = age >= 18;

  return (
    <div>
      <h1>안녕하세요, {name}님!</h1>
      <p>나이: {age}세</p>
      <p>상태: {isAdult ? '성인' : '미성년자'}</p>
      <p>내년 나이: {age + 1}세</p>
    </div>
  );
}
```

### 3. 속성(Attributes) 규칙

#### camelCase 사용

HTML의 kebab-case 대신 camelCase를 사용합니다.

```jsx
// HTML
<div class="container" tabindex="0" onclick="handleClick()">

// JSX
<div className="container" tabIndex="0" onClick={handleClick}>
```

#### 예약어 회피

JavaScript 예약어와 충돌하는 속성들은 다른 이름을 사용합니다.

```jsx
// HTML -> JSX
class -> className
for -> htmlFor
```

#### 속성값에 JavaScript 표현식 사용

```jsx
function Button({ disabled, type, onClick }) {
  return (
    <button className={`btn ${disabled ? 'disabled' : 'active'}`} type={type} onClick={onClick} disabled={disabled}>
      클릭하세요
    </button>
  );
}
```

### 4. 자식 요소 규칙

#### 텍스트 내용

```jsx
const element = <h1>Hello, World!</h1>;
```

#### 다른 JSX 요소

```jsx
const element = (
  <div>
    <h1>제목</h1>
    <p>내용</p>
  </div>
);
```

#### JavaScript 표현식

```jsx
function ItemList({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}
```

### 5. 주석 작성법

```jsx
function MyComponent() {
  return (
    <div>
      {/* JSX 내부 주석 */}
      <h1>제목</h1>

      {/* 
        여러 줄 주석도 가능
        이렇게 작성할 수 있습니다
      */}
      <p>내용</p>
    </div>
  );
}
```

## JSX에서 자주 사용하는 패턴

### 1. 조건부 렌더링

#### if문 사용 (JSX 외부)

```jsx
function UserGreeting({ user }) {
  if (!user) {
    return <div>로그인해주세요</div>;
  }

  return <div>안녕하세요, {user.name}님!</div>;
}
```

#### 삼항 연산자

```jsx
function UserStatus({ user }) {
  return <div>{user ? <span>환영합니다, {user.name}님!</span> : <span>로그인해주세요</span>}</div>;
}
```

#### 논리 AND 연산자

```jsx
function Notification({ message, show }) {
  return (
    <div>
      {show && <div className='alert'>{message}</div>}
      {message && message.length > 0 && <p>메시지 길이: {message.length}</p>}
    </div>
  );
}
```

### 2. 리스트 렌더링

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          <span className={todo.completed ? 'completed' : ''}>{todo.text}</span>
        </li>
      ))}
    </ul>
  );
}
```

### 3. 이벤트 핸들링

```jsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('로그인 시도:', { email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type='email' value={email} onChange={(e) => setEmail(e.target.value)} placeholder='이메일' />
      <input type='password' value={password} onChange={(e) => setPassword(e.target.value)} placeholder='비밀번호' />
      <button type='submit'>로그인</button>
    </form>
  );
}
```

### 4. 스타일 적용

#### CSS 클래스

```jsx
function Button({ variant, size }) {
  return <button className={`btn btn-${variant} btn-${size}`}>버튼</button>;
}
```

#### 인라인 스타일

```jsx
function Card({ backgroundColor, padding }) {
  const style = {
    backgroundColor: backgroundColor || '#fff',
    padding: padding || '1rem',
    borderRadius: '8px',
    boxShadow: '0 2px 4px rgba(0,0,0,0.1)',
  };

  return <div style={style}>카드 내용</div>;
}
```

#### 조건부 스타일

```jsx
function StatusBadge({ status }) {
  return (
    <span
      className={`badge ${
        status === 'active' ? 'badge-success' : status === 'inactive' ? 'badge-danger' : 'badge-warning'
      }`}
    >
      {status}
    </span>
  );
}
```

## JSX의 특수한 속성들

### 1. key 속성

리스트를 렌더링할 때 각 요소를 구분하기 위해 사용합니다.

```jsx
function ItemList({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>
          {' '}
          {/* 고유한 key 값 필수 */}
          {item.name}
        </li>
      ))}
    </ul>
  );
}
```

### 2. ref 속성

DOM 요소에 직접 접근하기 위해 사용합니다.

```jsx
function FocusInput() {
  const inputRef = useRef(null);

  const handleClick = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} type='text' />
      <button onClick={handleClick}>포커스</button>
    </div>
  );
}
```

### 3. dangerouslySetInnerHTML

HTML 문자열을 직접 삽입할 때 사용합니다. (보안 주의!)

```jsx
function HTMLContent({ htmlString }) {
  return <div dangerouslySetInnerHTML={{ __html: htmlString }} />;
}
```

## JSX 사용 시 주의사항

### 1. 보안 (XSS 방지)

JSX는 기본적으로 값을 이스케이프합니다.

```jsx
const userInput = '<script>alert("XSS")</script>';

// 안전함 - 문자열로 렌더링됨
function SafeComponent() {
  return <div>{userInput}</div>;
}

// 위험함 - 실제 HTML로 실행됨
function DangerousComponent() {
  return <div dangerouslySetInnerHTML={{ __html: userInput }} />;
}
```

### 2. 성능 최적화

#### 인라인 함수 피하기

```jsx
// 피해야 할 패턴 - 매번 새로운 함수 생성
function TodoItem({ todo }) {
  return <li onClick={() => handleClick(todo.id)}>{todo.text}</li>;
}

// 권장 패턴
function TodoItem({ todo, onTodoClick }) {
  return <li onClick={onTodoClick}>{todo.text}</li>;
}
```

#### 인라인 객체 피하기

```jsx
// 피해야 할 패턴
function MyComponent() {
  return <div style={{ marginTop: '10px', padding: '5px' }}>내용</div>;
}

// 권장 패턴
const styles = { marginTop: '10px', padding: '5px' };

function MyComponent() {
  return <div style={styles}>내용</div>;
}
```

### 3. 접근성 (a11y)

```jsx
function AccessibleForm() {
  return (
    <form>
      <label htmlFor='name'>이름:</label>
      <input id='name' type='text' aria-required='true' aria-describedby='name-help' />
      <div id='name-help'>이름을 입력해주세요</div>

      <button type='submit' aria-label='폼 제출'>
        제출
      </button>
    </form>
  );
}
```

## JSX 없이 React 사용하기

JSX는 필수가 아닙니다. `React.createElement`를 직접 사용할 수도 있습니다.

```jsx
// JSX 사용
function App() {
  return (
    <div className='app'>
      <h1>Hello</h1>
      <p>World</p>
    </div>
  );
}

// JSX 없이 사용
function App() {
  return React.createElement(
    'div',
    { className: 'app' },
    React.createElement('h1', null, 'Hello'),
    React.createElement('p', null, 'World')
  );
}
```

## 고급 JSX 패턴

### 1. 컴포넌트 조합

```jsx
function Card({ children }) {
  return <div className='card'>{children}</div>;
}

function CardHeader({ children }) {
  return <div className='card-header'>{children}</div>;
}

function CardBody({ children }) {
  return <div className='card-body'>{children}</div>;
}

// 사용법
function ProfileCard() {
  return (
    <Card>
      <CardHeader>
        <h2>사용자 프로필</h2>
      </CardHeader>
      <CardBody>
        <p>사용자 정보...</p>
      </CardBody>
    </Card>
  );
}
```

### 2. Render Props 패턴

```jsx
function DataFetcher({ url, render }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(url)
      .then((response) => response.json())
      .then((data) => {
        setData(data);
        setLoading(false);
      });
  }, [url]);

  return render({ data, loading });
}

// 사용법
function App() {
  return (
    <DataFetcher
      url='/api/users'
      render={({ data, loading }) => (
        <div>
          {loading ? (
            <p>로딩 중...</p>
          ) : (
            <ul>
              {data.map((user) => (
                <li key={user.id}>{user.name}</li>
              ))}
            </ul>
          )}
        </div>
      )}
    />
  );
}
```

## 결론

JSX는 React 개발의 핵심 요소로, HTML과 JavaScript를 자연스럽게 결합할 수 있게 해줍니다.

**JSX의 주요 장점:**

- **직관적인 문법**: HTML과 비슷하여 학습하기 쉬움
- **JavaScript 통합**: 표현식과 로직을 자연스럽게 포함
- **타입 안정성**: TypeScript와 함께 사용하면 컴파일 타임 검사 가능
- **도구 지원**: 에디터에서 신택스 하이라이팅과 자동완성 지원

면접에서는 JSX의 변환 과정, 기본 문법 규칙, 그리고 성능과 보안 관련 주의사항을 설명할 수 있어야 합니다. 특히 JSX가 단순한 템플릿 언어가 아니라 JavaScript의 확장이라는 점을 강조하는 것이 중요합니다.
