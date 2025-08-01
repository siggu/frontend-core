---
sidebar_position: 1
---

# Virtual DOM

## Virtual DOM이란?

### Virtual DOM의 정의와 본질

Virtual DOM(가상 DOM)은 **실제 DOM의 가벼운 JavaScript 표현**입니다. 웹 페이지의 HTML 구조를 메모리에 JavaScript 객체 형태로 복사해 놓은 것이라고 생각하면 됩니다.

#### DOM이란 무엇인가?

Virtual DOM을 이해하기 전에, 먼저 DOM이 무엇인지 알아야 합니다.

**DOM**(**Document Object Model**)은 웹 페이지의 HTML 문서를 JavaScript가 조작할 수 있도록 만든 **인터페이스**입니다. 쉽게 말해, HTML을 JavaScript 객체로 표현한 것이라고 생각하면 됩니다.

예를 들어, 다음과 같은 HTML이 있다면:

```html
<div id="app">
  <h1>제목</h1>
  <p>내용</p>
</div>
```

브라우저는 이를 DOM 트리로 변환합니다. 각 HTML 태그는 DOM 노드가 되고, 이 노드들은 부모-자식 관계를 가진 트리 구조를 형성합니다.

#### DOM의 구조와 특징

**1. 트리 구조**:

DOM은 계층적인 트리 구조를 가집니다. 루트 노드(html)부터 시작해서 각 태그들이 노드가 되고, 중첩된 태그들은 자식 노드가 됩니다.

**2. 풍부한 속성과 메서드**:

각 DOM 노드는 수많은 속성과 메서드를 가지고 있습니다. 예를 들어, 하나의 div 요소도 300개가 넘는 속성을 가지고 있습니다. 이것이 DOM이 무거운 이유 중 하나입니다.

**3. 라이브 컬렉션**:

DOM은 '살아있는' 객체입니다. DOM을 변경하면 즉시 화면에 반영되며, 이 과정에서 브라우저는 많은 작업을 수행해야 합니다.

#### DOM 조작의 비용과 성능 문제

**1. 브라우저 렌더링 파이프라인**:

DOM을 변경하면 브라우저는 다음과 같은 과정을 거칩니다:

- **파싱**: HTML을 DOM 트리로 변환
- **스타일 계산**: CSS를 적용하여 각 요소의 스타일 결정
- **레이아웃(리플로우)**: 각 요소의 위치와 크기 계산
- **페인트(리페인트)**: 실제 픽셀로 그리기
- **합성**: 여러 레이어를 합쳐서 최종 화면 생성

**2. 리플로우(Reflow)의 비용**:

요소의 위치나 크기가 변경되면 **리플로우**가 발생합니다. 이때 브라우저는:

- 변경된 요소의 새로운 위치와 크기를 계산
- 영향받는 다른 요소들의 위치도 다시 계산
- 전체 레이아웃을 다시 구성

**3. 리페인트(Repaint)의 비용**:

요소의 시각적 속성(색상, 배경 등)이 변경되면 **리페인트**가 발생합니다. 리플로우보다는 비용이 적지만 여전히 성능에 영향을 줍니다.

#### DOM 조작의 실제 문제점

**1. 빈번한 DOM 조작의 문제**

```javascript
// 문제가 있는 코드 - 매번 리플로우 발생
for (let i = 0; i < 1000; i++) {
  const div = document.createElement('div');
  div.innerHTML = `Item ${i}`;
  document.body.appendChild(div); // 1000번의 DOM 조작!
}
```

위 코드는 1000번의 DOM 조작을 수행하므로, 최대 1000번의 리플로우가 발생할 수 있습니다.

**2. 예측하기 어려운 성능**:

작은 변경이 큰 성능 문제를 일으킬 수 있습니다. 예를 들어, 리스트의 첫 번째 항목을 삭제하면 나머지 모든 항목의 위치가 다시 계산되어야 합니다.

**3. 동기적 처리**:

DOM 조작은 메인 스레드에서 동기적으로 처리되므로, 복잡한 DOM 조작 중에는 사용자 인터페이스가 멈출 수 있습니다.

#### Virtual DOM이 필요한 이유

이러한 DOM의 문제점들 때문에 Virtual DOM이 필요합니다:

**1. 배치 처리**:

여러 변경사항을 모아서 한 번에 DOM에 적용하여 리플로우/리페인트 횟수를 최소화합니다.

**2. 최적화된 업데이트**:

정말 변경이 필요한 부분만 찾아서 업데이트하므로, 불필요한 DOM 조작을 피할 수 있습니다.

**3. 예측 가능한 성능**:

Virtual DOM을 통해 업데이트 과정을 제어할 수 있어, 성능을 예측하고 최적화하기 쉽습니다.

Virtual DOM은 이 문제를 해결하기 위해 **중간 계층**을 제공합니다. 개발자는 직접 DOM을 건드리지 않고, Virtual DOM이라는 가상의 표현을 통해 작업합니다. 그러면 React가 알아서 실제 DOM과의 차이점을 찾아 필요한 부분만 업데이트합니다.

#### Virtual DOM의 핵심 특징

**1. 불변성(Immutability)**:

Virtual DOM 객체는 한 번 생성되면 변경되지 않습니다. 상태가 바뀔 때마다 완전히 새로운 Virtual DOM 트리를 만들어냅니다. 이는 예측 가능하고 안전한 업데이트를 보장합니다.

**2. 선언적 프로그래밍**:

개발자는 "현재 상태에서 UI가 어떻게 보여야 하는지"만 선언하면 됩니다. "어떻게 변경할지"는 React가 알아서 처리합니다.

**3. 추상화**:

복잡한 DOM 조작을 간단한 JavaScript 객체 조작으로 바꿔줍니다. 크로스 브라우저 호환성 문제도 React가 알아서 처리합니다.

### Virtual DOM의 내부 구조

Virtual DOM은 실제로는 단순한 JavaScript 객체입니다. 이 객체들이 트리 구조를 이루어 HTML의 구조를 흉내 냅니다.

#### Virtual DOM 객체의 기본 구성요소

모든 Virtual DOM 객체는 다음과 같은 기본 정보를 가지고 있습니다:

**1. type (타입)**

- HTML 요소라면 태그 이름 ('div', 'span', 'button' 등)
- React 컴포넌트라면 컴포넌트 함수나 클래스

**2. props (속성)**

- HTML 속성들 (className, id, style 등)
- 이벤트 핸들러 (onClick, onChange 등)
- children (자식 요소들)

**3. key (키)**

- 리스트 렌더링에서 각 요소를 구분하는 고유 식별자
- React가 어떤 요소가 추가/제거/이동되었는지 판단하는 데 사용

**4. ref (참조)**

- 실제 DOM 요소에 직접 접근해야 할 때 사용하는 참조

#### Virtual DOM 노드의 종류

**HTML 요소 노드**:

버튼, div, span 같은 일반적인 HTML 태그들입니다. type에는 태그 이름이 문자열로 들어갑니다.

**컴포넌트 노드**:

개발자가 만든 React 컴포넌트들입니다. type에는 함수나 클래스가 들어갑니다.

**텍스트 노드**:

가장 단순한 형태로, 그냥 문자열이나 숫자입니다. 별도의 객체 구조가 필요 없습니다.

**Fragment 노드**:

여러 요소를 그룹화하지만 실제 DOM에는 추가 요소를 만들지 않는 특수한 노드입니다.

### Virtual DOM 트리 구조와 노드 관계

#### 트리 구조의 형성

Virtual DOM은 계층적 트리 구조를 형성하며, 각 노드는 부모-자식 관계를 가집니다:

```javascript
// 복잡한 트리 구조 예제
const appTree = {
  type: 'div',
  props: {
    className: 'app',
    children: [
      // Header 섹션
      {
        type: 'header',
        props: {
          className: 'app-header',
          children: [
            {
              type: 'h1',
              props: { children: 'My App' },
            },
            {
              type: 'nav',
              props: {
                children: [
                  { type: 'a', props: { href: '/home', children: 'Home' } },
                  { type: 'a', props: { href: '/about', children: 'About' } },
                ],
              },
            },
          ],
        },
      },
      // Main 섹션
      {
        type: 'main',
        props: {
          className: 'app-main',
          children: [
            {
              type: UserProfile, // 사용자 정의 컴포넌트
              props: {
                userId: 123,
                userName: 'John Doe',
              },
            },
          ],
        },
      },
    ],
  },
};
```

#### 메모리에서 Virtual DOM이 관리되는 방식

##### 1. React의 내부 표현 (Fiber)

React 16 이후부터는 Virtual DOM을 Fiber라는 내부 구조로 변환하여 관리합니다:

```javascript
// Fiber 노드의 구조
const fiberNode = {
  // 노드 타입과 태그
  tag: 5, // HTML 요소의 경우 5
  type: 'div', // 요소 타입
  elementType: 'div', // 원본 요소 타입

  // props와 state
  props: { className: 'container' },
  memoizedProps: { className: 'container' }, // 이전 렌더링의 props
  memoizedState: null, // 현재 상태
  pendingProps: null, // 대기 중인 props

  // 트리 구조 정보
  child: null, // 첫 번째 자식 노드
  sibling: null, // 다음 형제 노드
  return: null, // 부모 노드
  index: 0, // 부모의 자식 중 인덱스

  // DOM 관련
  stateNode: null, // 실제 DOM 노드 참조

  // 업데이트 관련
  lanes: 0, // 업데이트 우선순위
  childLanes: 0, // 자식의 우선순위
  alternate: null, // 이전 버전의 fiber (더블 버퍼링)

  // 효과(Effect) 관련
  flags: 0, // 수행할 작업 플래그
  subtreeFlags: 0, // 서브트리의 플래그
  deletions: null, // 삭제할 자식들

  // 업데이트 큐
  updateQueue: null, // 상태 업데이트 큐

  // 개발자 도구용
  _debugOwner: null,
  _debugSource: null,
  _debugHookTypes: null,
};
```

##### 2. 더블 버퍼링 (Double Buffering)

React는 성능 최적화를 위해 두 개의 Fiber 트리를 유지합니다:

```javascript
// 현재 화면에 표시되는 트리 (Current Tree)
const currentTree = {
  type: 'div',
  props: { children: 'Old Content' },
  alternate: workInProgressTree, // 작업 중인 트리 참조
};

// 업데이트 작업 중인 트리 (Work-in-Progress Tree)
const workInProgressTree = {
  type: 'div',
  props: { children: 'New Content' },
  alternate: currentTree, // 현재 트리 참조
};

// 업데이트 완료 후 트리 교체
function commitWork() {
  // Work-in-Progress 트리가 새로운 Current 트리가 됨
  root.current = workInProgressTree;

  // 이전 Current 트리는 새로운 Work-in-Progress 트리가 됨
  workInProgressTree.alternate = currentTree;
}
```

### JSX에서 Virtual DOM 변환 과정

#### 1. JSX 파싱 단계

```jsx
// 개발자가 작성한 JSX
function WelcomeMessage({ name, isLoggedIn }) {
  return (
    <div className='welcome'>
      <h1>Welcome {name}!</h1>
      {isLoggedIn && <button onClick={handleLogout}>Logout</button>}
    </div>
  );
}
```

#### 2. Babel 변환 단계

```javascript
// Babel이 변환한 JavaScript (React 17 이전)
function WelcomeMessage({ name, isLoggedIn }) {
  return React.createElement(
    'div',
    { className: 'welcome' },
    React.createElement('h1', null, 'Welcome ', name, '!'),
    isLoggedIn && React.createElement('button', { onClick: handleLogout }, 'Logout')
  );
}

// React 17+ 새로운 JSX Transform
import { jsx as _jsx, jsxs as _jsxs } from 'react/jsx-runtime';

function WelcomeMessage({ name, isLoggedIn }) {
  return _jsxs('div', {
    className: 'welcome',
    children: [
      _jsxs('h1', { children: ['Welcome ', name, '!'] }),
      isLoggedIn &&
        _jsx('button', {
          onClick: handleLogout,
          children: 'Logout',
        }),
    ],
  });
}
```

#### 3. React.createElement() 함수의 내부 동작

```javascript
// React.createElement의 단순화된 구현
function createElement(type, config, ...children) {
  let props = {};
  let key = null;
  let ref = null;

  // config에서 props 추출
  if (config != null) {
    if (config.key !== undefined) {
      key = '' + config.key;
    }
    if (config.ref !== undefined) {
      ref = config.ref;
    }

    // 나머지 속성들을 props에 복사
    for (let propName in config) {
      if (config.hasOwnProperty(propName) && propName !== 'key' && propName !== 'ref') {
        props[propName] = config[propName];
      }
    }
  }

  // children 처리
  const childrenLength = children.length;
  if (childrenLength === 1) {
    props.children = children[0];
  } else if (childrenLength > 1) {
    props.children = children;
  }

  // Virtual DOM 요소 생성
  return {
    $$typeof: REACT_ELEMENT_TYPE,
    type: type,
    key: key,
    ref: ref,
    props: props,
    _owner: ReactCurrentOwner.current,
  };
}
```

#### 4. 조건부 렌더링과 리스트의 Virtual DOM 표현

```javascript
// 조건부 렌더링
const conditionalVDOM = {
  type: 'div',
  props: {
    children: [
      { type: 'h1', props: { children: 'Title' } },
      // 조건이 false이면 null 또는 undefined
      isVisible ? { type: 'p', props: { children: 'Content' } } : null,
    ].filter(Boolean), // null 값들 제거
  },
};

// 리스트 렌더링
const listVDOM = {
  type: 'ul',
  props: {
    children: items.map((item, index) => ({
      type: 'li',
      key: item.id, // 중요: 고유한 key
      props: {
        children: item.name,
      },
    })),
  },
};
```

### 핵심 개념의 심화 이해

#### 1. 불변성(Immutability)의 중요성

```javascript
// 잘못된 방식: 기존 객체 변경
const oldVDOM = { type: 'div', props: { children: 'Old' } };
oldVDOM.props.children = 'New'; // 이렇게 하면 안됨!

// 올바른 방식: 새로운 객체 생성
const newVDOM = {
  ...oldVDOM,
  props: {
    ...oldVDOM.props,
    children: 'New',
  },
};
```

#### 2. 참조 동등성 vs 값 동등성

```javascript
// 참조 동등성 검사 (React가 실제로 사용하는 방식)
const element1 = { type: 'div', props: { children: 'Hello' } };
const element2 = { type: 'div', props: { children: 'Hello' } };

console.log(element1 === element2); // false (다른 객체)

// React는 이를 해결하기 위해 세밀한 비교를 수행
function shallowEqual(obj1, obj2) {
  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);

  if (keys1.length !== keys2.length) {
    return false;
  }

  for (let key of keys1) {
    if (obj1[key] !== obj2[key]) {
      return false;
    }
  }

  return true;
}
```

#### 3. 메모리 내 표현의 최적화

```javascript
// React의 요소 풀링 (Element Pooling) - 이전 버전에서 사용
const elementPool = [];

function createOptimizedElement(type, props) {
  // 풀에서 재사용 가능한 객체 찾기
  let element = elementPool.pop();

  if (!element) {
    // 풀이 비어있으면 새로 생성
    element = {
      $$typeof: REACT_ELEMENT_TYPE,
      type: null,
      props: null,
      key: null,
      ref: null,
    };
  }

  // 속성 설정
  element.type = type;
  element.props = props;

  return element;
}

function releaseElement(element) {
  // 사용 완료된 요소를 풀에 반환
  element.type = null;
  element.props = null;
  elementPool.push(element);
}
```

```javascript
// 실제 DOM 요소
<div id="container">
  <h1>Hello World</h1>
  <p>Virtual DOM 예제</p>
</div>

// Virtual DOM 표현 (단순화된 형태)
{
  type: 'div',
  props: {
    id: 'container',
    children: [
      {
        type: 'h1',
        props: {
          children: 'Hello World'
        }
      },
      {
        type: 'p',
        props: {
          children: 'Virtual DOM 예제'
        }
      }
    ]
  }
}
```

## Real DOM vs Virtual DOM

### Real DOM의 특징과 문제점

#### Real DOM의 구조

```javascript
// Real DOM 직접 조작 예제
const element = document.createElement('div');
element.className = 'container';
element.innerHTML = '<h1>Title</h1><p>Content</p>';
document.body.appendChild(element);

// 업데이트 시
element.innerHTML = '<h1>New Title</h1><p>New Content</p>';
```

#### Real DOM의 문제점

1. **느린 성능**: DOM 조작은 브라우저에서 가장 비용이 많이 드는 작업 중 하나
2. **리플로우와 리페인트**: DOM 변경 시 전체 페이지의 레이아웃 재계산 발생
3. **메모리 사용량**: DOM 노드는 많은 속성과 메서드를 포함하여 무겁습니다
4. **동기적 처리**: DOM 조작은 메인 스레드를 블로킹합니다

```javascript
// 성능 문제를 보여주는 예제
for (let i = 0; i < 1000; i++) {
  const div = document.createElement('div');
  div.textContent = `Item ${i}`;
  document.body.appendChild(div); // 매번 리플로우 발생
}
```

### Virtual DOM의 장점

#### 1. 성능 최적화

```javascript
// Virtual DOM은 배치 업데이트를 통해 성능 최적화
const virtualElements = [];
for (let i = 0; i < 1000; i++) {
  virtualElements.push({
    type: 'div',
    props: { children: `Item ${i}` },
  });
}
// 한 번에 실제 DOM에 적용
```

#### 2. 예측 가능한 업데이트

- 상태가 변경될 때마다 전체 Virtual DOM 트리를 다시 생성
- 이전 트리와 비교하여 실제로 변경된 부분만 찾아내어 업데이트

#### 3. 배치 업데이트

- 여러 상태 변경을 모아서 한 번에 처리
- 불필요한 리렌더링 방지

## Virtual DOM의 동작 원리

Virtual DOM이 어떻게 작동하는지 이해하는 것은 React를 제대로 사용하기 위해 매우 중요합니다. 전체 과정을 쉽게 설명하면 다음과 같습니다.

### 전체 동작 흐름

Virtual DOM의 작동은 6단계로 나눌 수 있습니다:

**1단계: 상태 변경 감지**:

사용자가 버튼을 클릭하거나, 데이터가 변경되는 등의 상황에서 상태 변경이 감지됩니다.

**2단계: 새로운 Virtual DOM 트리 생성**:

변경된 상태를 바탕으로 새로운 Virtual DOM 트리를 만듭니다. 이때 이전 트리는 그대로 유지됩니다.

**3단계: Diffing (비교) 알고리즘 실행**:

이전 Virtual DOM 트리와 새로운 Virtual DOM 트리를 비교해서 무엇이 바뀌었는지 찾아냅니다.

**4단계: Reconciliation (재조정) 수행**:

바뀐 부분들을 어떻게 실제 DOM에 적용할지 계획을 세웁니다.

**5단계: 실제 DOM 업데이트**:

계획한 대로 실제 DOM을 수정합니다. 이때 바뀐 부분만 업데이트됩니다.

**6단계: 생명주기 메서드 및 Effects 실행**:

업데이트가 완료된 후 필요한 부수 효과들(API 호출, 포커스 이동 등)을 실행합니다.

### 상태 변경 감지와 스케줄링

#### 상태 변경이 시작되는 시점

상태 변경은 주로 다음과 같은 상황에서 발생합니다:

**사용자 인터랙션**: 버튼 클릭, 텍스트 입력, 마우스 이벤트 등에서 `setState`나 `useState`의 setter 함수가 호출될 때

**비동기 작업 완료**: API 호출 결과를 받아서 상태를 업데이트할 때, 타이머가 끝날 때

**Props 변경**: 부모 컴포넌트에서 전달받은 props가 바뀔 때

#### React의 스마트한 업데이트 스케줄링

React는 모든 상태 변경을 즉시 처리하지 않습니다. 대신 **스케줄링 시스템**을 통해 효율적으로 관리합니다.

**배치 처리**: 같은 이벤트에서 발생하는 여러 상태 변경을 모아서 한 번에 처리합니다. 예를 들어, 버튼 클릭 시 여러 `setState`를 호출해도 리렌더링은 한 번만 발생합니다.

**우선순위 시스템**: 사용자 입력처럼 중요한 업데이트는 즉시 처리하고, 백그라운드 작업은 나중에 처리합니다.

**중단 가능한 렌더링**: 더 중요한 작업이 생기면 현재 렌더링을 중단하고 우선순위가 높은 작업을 먼저 처리합니다.

### 렌더링 단계 - Virtual DOM 트리 생성

#### 컴포넌트 함수 실행과 Virtual DOM 생성

상태가 변경되면 React는 관련된 컴포넌트들을 다시 실행합니다. 이 과정에서 새로운 Virtual DOM 트리가 만들어집니다.

**함수형 컴포넌트의 경우**: 컴포넌트 함수를 다시 호출하여 새로운 JSX를 반환받습니다.

**클래스 컴포넌트의 경우**: `render()` 메서드를 다시 호출하여 새로운 JSX를 반환받습니다.

#### Hook 시스템의 작동 방식

Hook들은 컴포넌트가 렌더링될 때마다 **순서대로** 실행됩니다. React는 각 Hook의 상태를 **연결 리스트** 형태로 관리합니다.

**첫 번째 렌더링**: Hook들이 초기화되고 초기값이 설정됩니다.

**이후 렌더링**: 저장된 Hook 상태들을 순서대로 가져와서 업데이트된 값을 반환합니다.

이것이 바로 Hook을 조건문이나 반복문 안에서 사용하면 안 되는 이유입니다. Hook의 호출 순서가 바뀌면 React가 상태를 잘못 연결할 수 있기 때문입니다.

#### 자식 컴포넌트 처리와 재귀적 렌더링

컴포넌트가 렌더링되면서 자식 요소들이 생성되는데, 이때 React는 **재귀적으로** 모든 자식들을 처리합니다.

**자식 요소 탐색**: 부모 컴포넌트에서 반환된 JSX의 자식들을 하나씩 검사합니다.

**기존 요소와 비교**: 이전 렌더링에서 같은 위치에 있던 요소와 새로운 요소를 비교합니다.

**재사용 또는 새 생성**: 타입과 key가 같으면 기존 요소를 재사용하고, 다르면 새로 만듭니다.

**트리 구조 연결**: 새로 만들어진 Virtual DOM 노드들을 부모-자식, 형제 관계로 연결합니다.

### Diffing 알고리즘 - 변경사항 찾기

Diffing은 이전 Virtual DOM 트리와 새로운 Virtual DOM 트리를 비교해서 **최소한의 변경사항**을 찾아내는 과정입니다.

#### Diffing의 핵심 원칙

**같은 타입이면 업데이트, 다른 타입이면 교체**
`<div>`에서 `<span>`으로 바뀌면 기존 요소를 완전히 제거하고 새로 만듭니다.

**key를 활용한 효율적인 비교**
리스트에서 요소들의 순서가 바뀌어도 key가 같으면 이동으로 처리합니다.

**깊이 우선 탐색**
트리의 위쪽부터 아래쪽으로, 왼쪽부터 오른쪽으로 순서대로 비교합니다.

#### 3-2. Props 비교 알고리즘

```javascript
// Props 비교의 상세 구현
function diffProps(prevProps, nextProps) {
  const changes = [];
  const allKeys = new Set([...Object.keys(prevProps || {}), ...Object.keys(nextProps || {})]);

  for (let key of allKeys) {
    const prevValue = prevProps?.[key];
    const nextValue = nextProps?.[key];

    // 특수 속성들은 별도 처리
    if (key === 'children') continue;

    if (prevValue !== nextValue) {
      // 이벤트 핸들러 특별 처리
      if (key.startsWith('on')) {
        changes.push({
          type: 'EVENT_LISTENER',
          key: key.toLowerCase().substring(2), // onClick -> click
          prevHandler: prevValue,
          nextHandler: nextValue,
        });
      }
      // 스타일 객체 특별 처리
      else if (key === 'style') {
        const styleDiff = diffStyles(prevValue, nextValue);
        if (styleDiff.length > 0) {
          changes.push({
            type: 'STYLE',
            changes: styleDiff,
          });
        }
      }
      // 일반 속성
      else {
        changes.push({
          type: 'ATTRIBUTE',
          key: key,
          prevValue: prevValue,
          nextValue: nextValue,
        });
      }
    }
  }

  return changes;
}

// 스타일 객체 비교
function diffStyles(prevStyle, nextStyle) {
  const changes = [];
  const allKeys = new Set([...Object.keys(prevStyle || {}), ...Object.keys(nextStyle || {})]);

  for (let key of allKeys) {
    const prevValue = prevStyle?.[key];
    const nextValue = nextStyle?.[key];

    if (prevValue !== nextValue) {
      changes.push({
        property: key,
        prevValue: prevValue,
        nextValue: nextValue,
      });
    }
  }

  return changes;
}
```

#### 3-3. 리스트 렌더링과 Key 기반 Diffing

```javascript
// Key를 활용한 효율적인 리스트 Diffing
function diffChildrenWithKeys(prevChildren, nextChildren) {
  const changes = [];

  // 1. Key로 매핑 생성
  const prevChildrenByKey = new Map();
  const nextChildrenByKey = new Map();

  prevChildren.forEach((child, index) => {
    const key = child.key !== null ? child.key : index;
    prevChildrenByKey.set(key, { child, index });
  });

  nextChildren.forEach((child, index) => {
    const key = child.key !== null ? child.key : index;
    nextChildrenByKey.set(key, { child, index });
  });

  // 2. 삭제된 요소 찾기
  for (let [key, { child, index }] of prevChildrenByKey) {
    if (!nextChildrenByKey.has(key)) {
      changes.push({
        type: 'DELETE',
        key: key,
        index: index,
        element: child,
      });
    }
  }

  // 3. 추가 및 이동된 요소 처리
  for (let [key, { child, index }] of nextChildrenByKey) {
    const prevItem = prevChildrenByKey.get(key);

    if (!prevItem) {
      // 새로운 요소
      changes.push({
        type: 'INSERT',
        key: key,
        index: index,
        element: child,
      });
    } else if (prevItem.index !== index) {
      // 위치 변경된 요소
      changes.push({
        type: 'MOVE',
        key: key,
        fromIndex: prevItem.index,
        toIndex: index,
        element: child,
      });

      // 요소 자체의 변경사항도 확인
      const elementDiff = diff(prevItem.child, child);
      if (elementDiff) {
        changes.push({
          type: 'UPDATE_MOVED',
          key: key,
          index: index,
          changes: elementDiff,
        });
      }
    } else {
      // 같은 위치의 요소 업데이트
      const elementDiff = diff(prevItem.child, child);
      if (elementDiff) {
        changes.push({
          type: 'UPDATE',
          key: key,
          index: index,
          changes: elementDiff,
        });
      }
    }
  }

  return changes;
}
```

### 4. Work Loop와 타임 슬라이싱

#### 4-1. 인터럽트 가능한 렌더링

```javascript
// React의 타임 슬라이싱 구현 (개념적)
const WORK_UNIT_TIME = 5; // 5ms씩 작업 수행

function workLoop(deadline) {
  let shouldYield = false;

  while (nextUnitOfWork && !shouldYield) {
    nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
    shouldYield = deadline.timeRemaining() < 1;
  }

  // 더 할 일이 있으면 다음 프레임에서 계속
  if (nextUnitOfWork) {
    requestIdleCallback(workLoop);
  } else if (pendingCommit) {
    // 렌더링 완료 -> 커밋 단계로
    commitPhase(pendingCommit);
  }
}

function performUnitOfWork(fiber) {
  // 1. 현재 fiber 처리
  if (fiber.tag === 'FUNCTION_COMPONENT') {
    updateFunctionComponent(fiber);
  } else if (fiber.tag === 'HOST_COMPONENT') {
    updateHostComponent(fiber);
  }

  // 2. 다음 작업 단위 결정
  if (fiber.child) {
    return fiber.child; // 자식 먼저 처리
  }

  let nextFiber = fiber;
  while (nextFiber) {
    if (nextFiber.sibling) {
      return nextFiber.sibling; // 형제 처리
    }
    nextFiber = nextFiber.return; // 부모로 올라가기
  }

  return null; // 작업 완료
}
```

#### 4-2. 우선순위 기반 스케줄링

```javascript
// 업데이트 우선순위 시스템
const Priority = {
  IMMEDIATE: 1, // 사용자 입력 (클릭, 타이핑)
  USER_BLOCKING: 2, // 마우스 오버, 스크롤
  NORMAL: 3, // 네트워크 응답, 타이머
  LOW: 4, // 오프스크린 업데이트
  IDLE: 5, // 분석, 프리페칭
};

function scheduleUpdateWithPriority(fiber, update, priority) {
  update.priority = priority;
  update.expirationTime = computeExpirationTime(priority);

  // 우선순위별 큐에 추가
  addToUpdateQueue(fiber, update);

  // 현재 작업보다 높은 우선순위면 인터럽트
  if (priority < currentWorkPriority) {
    interruptCurrentWork();
    scheduleHighPriorityWork(fiber);
  }
}

function computeExpirationTime(priority) {
  const now = getCurrentTime();

  switch (priority) {
    case Priority.IMMEDIATE:
      return now; // 즉시 처리
    case Priority.USER_BLOCKING:
      return now + 250; // 250ms 내
    case Priority.NORMAL:
      return now + 5000; // 5초 내
    case Priority.LOW:
      return now + 10000; // 10초 내
    case Priority.IDLE:
      return now + 30000; // 30초 내
    default:
      return now + 5000;
  }
}
```

### 초기 렌더링

```jsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <div className="app">
      <h1>Counter: {count}</h1>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

// 위 JSX가 Virtual DOM으로 변환되는 과정
React.createElement(
  'div',
  { className: 'app' },
  React.createElement('h1', null, `Counter: ${count}`),
  React.createElement(
    'button',
    { onClick: () => setCount(count + 1) },
    'Increment'
  )
)

// 결과적인 Virtual DOM 객체
{
  type: 'div',
  props: {
    className: 'app',
    children: [
      {
        type: 'h1',
        props: {
          children: 'Counter: 0'
        }
      },
      {
        type: 'button',
        props: {
          onClick: [Function],
          children: 'Increment'
        }
      }
    ]
  }
}
```

#### 상태 업데이트 시

```javascript
// 상태가 변경되면 새로운 Virtual DOM 트리 생성
// count가 0에서 1로 변경될 때

// 이전 Virtual DOM
{
  type: 'h1',
  props: { children: 'Counter: 0' }
}

// 새로운 Virtual DOM
{
  type: 'h1',
  props: { children: 'Counter: 1' }
}
```

### 2. Diffing 알고리즘

React는 **O(n³)** 복잡도의 일반적인 트리 비교 알고리즘 대신, **O(n)** 복잡도의 휴리스틱 알고리즘을 사용합니다.

#### Diffing의 핵심 가정들

##### 1. 서로 다른 타입의 요소는 서로 다른 트리를 만든다

```jsx
// 이전
<div>
  <Counter />
</div>

// 이후
<span>
  <Counter />
</span>

// 결과: div 트리 전체를 제거하고 span 트리를 새로 생성
// Counter 컴포넌트도 완전히 새로 마운트됨
```

##### 2. key prop을 통해 어떤 자식 요소가 변경되지 않았는지 알 수 있다

```jsx
// key가 없는 경우 - 비효율적
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li> // 새로 추가
  <li>Duke</li>        // 내용이 변경됨 (실제로는 그대로)
  <li>Villanova</li>   // 내용이 변경됨 (실제로는 그대로)
</ul>

// key가 있는 경우 - 효율적
<ul>
  <li key="duke">Duke</li>
  <li key="villanova">Villanova</li>
</ul>

<ul>
  <li key="connecticut">Connecticut</li> // 새로 추가
  <li key="duke">Duke</li>               // 위치만 변경
  <li key="villanova">Villanova</li>     // 위치만 변경
</ul>
```

#### Diffing 과정의 상세 단계

##### 1. 요소 타입 비교

```javascript
// 타입이 다른 경우
function diffElementType(prevElement, nextElement) {
  if (prevElement.type !== nextElement.type) {
    // 완전히 새로운 요소로 교체
    return 'REPLACE';
  }
  return 'UPDATE';
}
```

##### 2. 속성(Props) 비교

```javascript
// 속성 변경 감지
function diffProps(prevProps, nextProps) {
  const updates = {};

  // 변경된 속성 찾기
  for (let key in nextProps) {
    if (prevProps[key] !== nextProps[key]) {
      updates[key] = nextProps[key];
    }
  }

  // 제거된 속성 찾기
  for (let key in prevProps) {
    if (!(key in nextProps)) {
      updates[key] = null;
    }
  }

  return updates;
}
```

##### 3. 자식 요소 비교

```javascript
// 자식 요소 비교 (단순화된 버전)
function diffChildren(prevChildren, nextChildren) {
  const patches = [];
  const maxLength = Math.max(prevChildren.length, nextChildren.length);

  for (let i = 0; i < maxLength; i++) {
    const prevChild = prevChildren[i];
    const nextChild = nextChildren[i];

    if (!prevChild && nextChild) {
      // 새로운 자식 추가
      patches.push({ type: 'INSERT', index: i, element: nextChild });
    } else if (prevChild && !nextChild) {
      // 자식 제거
      patches.push({ type: 'REMOVE', index: i });
    } else if (prevChild && nextChild) {
      // 자식 업데이트
      const childPatches = diff(prevChild, nextChild);
      if (childPatches.length > 0) {
        patches.push({ type: 'UPDATE', index: i, patches: childPatches });
      }
    }
  }

  return patches;
}
```

### 3. Reconciliation 과정

Reconciliation은 Virtual DOM의 변경사항을 실제 DOM에 적용하는 과정입니다.

#### Reconciliation의 단계

##### 1. 패치 생성

```javascript
// Diffing 결과로 생성되는 패치 예제
const patches = [
  {
    type: 'UPDATE_PROPS',
    element: h1Element,
    props: { children: 'Counter: 1' },
  },
  {
    type: 'INSERT',
    parent: ulElement,
    index: 0,
    element: newLiElement,
  },
];
```

##### 2. 실제 DOM 업데이트

```javascript
// 패치를 실제 DOM에 적용
function applyPatches(element, patches) {
  patches.forEach((patch) => {
    switch (patch.type) {
      case 'UPDATE_PROPS':
        updateProps(patch.element, patch.props);
        break;
      case 'INSERT':
        patch.parent.insertBefore(patch.element, patch.parent.children[patch.index]);
        break;
      case 'REMOVE':
        patch.element.parentNode.removeChild(patch.element);
        break;
      case 'REPLACE':
        patch.element.parentNode.replaceChild(patch.newElement, patch.element);
        break;
    }
  });
}
```

#### React Fiber와 Reconciliation

React 16부터 도입된 Fiber 아키텍처는 Reconciliation 과정을 더욱 효율적으로 만듭니다:

```javascript
// Fiber의 작업 단위
const fiberNode = {
  type: 'div',
  props: { className: 'container' },
  child: null, // 첫 번째 자식
  sibling: null, // 다음 형제
  return: null, // 부모
  alternate: null, // 이전 버전의 fiber
  effectTag: null, // 수행할 작업 타입
  updateQueue: null, // 상태 업데이트 큐
};
```

##### 작업 스케줄링

```javascript
// 우선순위 기반 스케줄링 (개념적 예제)
const TaskPriority = {
  IMMEDIATE: 1, // 사용자 입력
  NORMAL: 3, // 네트워크 응답
  LOW: 5, // 오프스크린 컴포넌트
  IDLE: 6, // 미사용 시간
};

function scheduleWork(fiber, priority) {
  if (priority === TaskPriority.IMMEDIATE) {
    // 즉시 실행
    performSyncWork(fiber);
  } else {
    // 스케줄러에 등록
    scheduleCallback(priority, () => performWork(fiber));
  }
}
```

## Virtual DOM의 장점과 한계점

### 장점

#### 1. 성능 최적화

```javascript
// 여러 상태 변경이 있을 때
function handleMultipleUpdates() {
  setName('John');
  setAge(25);
  setEmail('john@example.com');

  // Virtual DOM은 이 모든 변경사항을 배치로 처리
  // 실제 DOM 업데이트는 한 번만 발생
}
```

#### 2. 개발자 경험 향상

```jsx
// 선언적 프로그래밍
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id} className={todo.completed ? 'completed' : ''}>
          {todo.text}
        </li>
      ))}
    </ul>
  );
}

// 상태가 변경되면 React가 자동으로 UI 업데이트
// 개발자는 "어떻게" 업데이트할지 걱정할 필요 없음
```

#### 3. 크로스 브라우저 호환성

Virtual DOM은 브라우저 간의 차이점을 추상화하여 일관된 API를 제공합니다.

#### 4. 서버 사이드 렌더링 지원

```javascript
// 서버에서 Virtual DOM을 HTML 문자열로 변환
const html = ReactDOMServer.renderToString(<App />);
```

### 한계점

#### 1. 메모리 사용량

```javascript
// Virtual DOM도 메모리를 사용
// 복잡한 애플리케이션에서는 상당한 메모리 오버헤드 발생
const complexTree = {
  // 수천 개의 중첩된 객체들...
};
```

#### 2. 초기 성능 오버헤드

```javascript
// 단순한 DOM 조작의 경우 Virtual DOM이 더 느릴 수 있음
// 예: 단일 텍스트 노드 업데이트
document.getElementById('text').textContent = 'New Text'; // 빠름

// vs
function Component() {
  const [text, setText] = useState('Old Text');
  return <div>{text}</div>; // Virtual DOM 오버헤드
}
```

#### 3. 학습 곡선

Virtual DOM의 작동 원리를 이해하고 최적화하려면 추가적인 학습이 필요합니다.

## 실제 성능 비교 예제

### 1. 대량 데이터 렌더링

#### Vanilla JavaScript 방식

```javascript
function renderWithVanillaJS(data) {
  const startTime = performance.now();

  const container = document.getElementById('container');
  container.innerHTML = ''; // 전체 클리어

  data.forEach((item) => {
    const div = document.createElement('div');
    div.className = 'item';
    div.textContent = item.name;
    container.appendChild(div); // 매번 DOM 조작
  });

  const endTime = performance.now();
  console.log(`Vanilla JS: ${endTime - startTime}ms`);
}
```

#### React Virtual DOM 방식

```jsx
function ItemList({ data }) {
  return (
    <div id='container'>
      {data.map((item) => (
        <div key={item.id} className='item'>
          {item.name}
        </div>
      ))}
    </div>
  );
}

// React는 변경사항만 효율적으로 업데이트
function App() {
  const [data, setData] = useState([]);

  useEffect(() => {
    const startTime = performance.now();
    setData(generateLargeDataSet());

    // React가 렌더링 완료 후 측정
    setTimeout(() => {
      const endTime = performance.now();
      console.log(`React: ${endTime - startTime}ms`);
    }, 0);
  }, []);

  return <ItemList data={data} />;
}
```

### 2. 부분 업데이트 성능

```jsx
// 효율적인 업데이트 예제
function OptimizedList({ items }) {
  return (
    <div>
      {items.map((item) => (
        <MemoizedItem key={item.id} item={item} />
      ))}
    </div>
  );
}

// React.memo를 사용한 최적화
const MemoizedItem = React.memo(function Item({ item }) {
  console.log(`렌더링: ${item.name}`); // 변경된 아이템만 로그 출력

  return (
    <div className='item'>
      <h3>{item.name}</h3>
      <p>{item.description}</p>
    </div>
  );
});
```

### 3. 벤치마크 결과 (참고용)

```javascript
// 일반적인 성능 비교 결과 (1000개 요소 기준)
const benchmarkResults = {
  vanillaJS: {
    initialRender: '15ms',
    update: '12ms',
    memoryUsage: '2MB',
  },
  react: {
    initialRender: '25ms',
    update: '8ms', // 부분 업데이트에서 더 효율적
    memoryUsage: '5MB', // Virtual DOM으로 인한 오버헤드
  },
};
```

## 결론

Virtual DOM은 React의 핵심 혁신 중 하나로, 다음과 같은 가치를 제공합니다:

### 핵심 가치

1. **성능 최적화**: 불필요한 DOM 조작 최소화
2. **개발 편의성**: 선언적 프로그래밍 패러다임 제공
3. **예측 가능성**: 명확한 데이터 흐름과 상태 관리
4. **확장성**: 복잡한 애플리케이션에서의 우수한 성능

### 적절한 사용 시기

- 복잡한 사용자 인터페이스
- 빈번한 상태 변경이 있는 애플리케이션
- 여러 개발자가 협업하는 대규모 프로젝트
- 크로스 플랫폼 개발 (React Native)

Virtual DOM은 현대 웹 개발에서 UI 성능과 개발자 경험을 모두 향상시키는 핵심 기술입니다.
