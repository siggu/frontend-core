---
sidebar_position: 6
---

# state를 직접 변경하지 않는 이유

## 불변성 개념의 철학적 탄생 배경

### 함수형 프로그래밍에서 온 불변성 철학

**수학적 함수의 순수성에서 출발**
불변성(Immutability)의 개념은 수학의 함수에서 출발했습니다. 수학에서 함수 f(x) = y는 동일한 입력 x에 대해 항상 동일한 출력 y를 반환합니다. 이때 x는 변경되지 않으며, 새로운 값 y가 생성됩니다. 함수형 프로그래밍은 이러한 수학적 순수성을 소프트웨어에 적용한 패러다임입니다.

**부작용(Side Effect) 제거의 철학**
전통적인 명령형 프로그래밍에서는 데이터를 직접 변경(mutation)하는 것이 일반적이었습니다. 하지만 이는 예상치 못한 부작용을 일으켰습니다. 한 곳에서 데이터를 변경했을 때, 그 데이터를 참조하는 다른 모든 곳에서 예상치 못한 변화가 일어나는 것입니다.

**시간의 개념과 상태 변화**
불변성은 "시간"이라는 개념을 프로그래밍에 명확히 도입했습니다. 상태는 시간의 흐름에 따라 변화하지만, 각 시점의 상태 자체는 불변이라는 철학입니다.

### React 팀이 불변성을 채택한 근본적 이유

**Facebook의 대규모 애플리케이션 문제**
Facebook과 같은 대규모 애플리케이션에서는 상태 변화의 추적이 매우 어려웠습니다. 한 컴포넌트에서 객체를 변경했을 때, 그 객체를 공유하는 다른 컴포넌트들에서 예상치 못한 버그가 발생했습니다.

**"시간 여행 디버깅"의 필요성**
개발자들은 버그를 추적하기 위해 "상태가 언제, 어떻게 변경되었는지"를 알아야 했습니다. 하지만 상태를 직접 변경하면 변경의 기록이 남지 않아 디버깅이 거의 불가능했습니다.

**성능 최적화의 새로운 접근**
전통적인 깊은 비교(Deep Comparison)는 매우 비용이 많이 드는 작업이었습니다. React 팀은 "참조 비교"라는 단순하면서도 강력한 방법을 통해 성능과 정확성을 동시에 확보하고자 했습니다.

## React의 상태 변화 감지 메커니즘의 동작 원리

### 참조 동일성 기반 변화 감지의 심층 원리

**메모리 주소를 통한 비교**
JavaScript에서 객체와 배열은 메모리의 특정 위치에 저장됩니다. React는 이전 상태와 새로운 상태의 메모리 주소를 비교하여 변경을 감지합니다. 이는 Object.is() 함수와 유사한 방식으로 작동합니다.

**얕은 비교의 효율성**
React가 얕은 비교를 선택한 이유는 성능 때문입니다. 깊은 비교는 객체의 모든 속성을 재귀적으로 검사해야 하므로 O(n) 복잡도를 가지지만, 얕은 비교는 O(1) 복잡도로 매우 빠릅니다.

**React의 배치 처리(Batching) 메커니즘**
React는 상태 변경을 즉시 처리하지 않고 "배치"로 모아서 처리합니다. 이때 각 상태 변경의 참조를 비교하여 실제로 변경된 것만 리렌더링 큐에 추가합니다.

### Virtual DOM과 불변성의 상호작용

**Reconciliation 과정에서의 불변성 활용**
React의 Reconciliation 과정에서 불변성은 핵심적인 역할을 합니다. 이전 Virtual DOM 트리와 새로운 Virtual DOM 트리를 비교할 때, 참조가 동일한 객체는 변경되지 않았다고 가정하고 해당 서브트리 전체를 건너뛸 수 있습니다.

**Fiber 아키텍처와 불변성**
React 16의 Fiber 아키텍처에서는 작업을 중단하고 재시작할 수 있습니다. 이때 불변성이 보장되어야만 이전 상태로 안전하게 되돌아갈 수 있습니다.

## 불변성 위반 시 발생하는 근본적 문제들

### React의 렌더링 메커니즘 파괴

**리렌더링 스킵 현상의 원리**
React는 상태 객체의 참조가 동일하면 "변경이 없다"고 판단하고 리렌더링을 건너뜁니다. 이는 성능 최적화를 위한 설계이지만, 불변성을 위반하면 UI와 실제 데이터 간의 불일치가 발생합니다.

**컴포넌트 트리 전파 차단**
React의 렌더링은 트리 구조로 전파됩니다. 부모 컴포넌트에서 불변성을 위반하면, 자식 컴포넌트들도 업데이트되지 않아 전체 UI 상태가 일관성을 잃게 됩니다.

### 메모리 최적화 기능 무력화

**React.memo의 최적화 실패**
React.memo는 props의 참조 비교를 통해 리렌더링을 방지합니다. 불변성을 위반하면 이러한 메모이제이션이 작동하지 않아 불필요한 리렌더링이 발생합니다.

**useMemo와 useCallback의 의존성 추적 실패**
이 Hook들은 의존성 배열의 참조 변화를 감지하여 메모이제이션을 제어합니다. 불변성 위반 시 의존성 변화를 감지하지 못해 최적화가 무효화됩니다.

## 불변성이 가능하게 하는 고급 기능들

### 시간 여행 디버깅의 구현 원리

**상태 스냅샷의 보존**
불변성을 통해 각 시점의 상태가 독립적인 객체로 존재하므로, 이전 상태들을 모두 보존할 수 있습니다. Redux DevTools의 시간 여행 디버깅이 가능한 이유입니다.

**Undo/Redo 시스템의 자연스러운 구현**
불변성을 지키면 상태 변화의 히스토리를 스택으로 관리할 수 있어, Undo/Redo 기능을 자연스럽게 구현할 수 있습니다.

### 동시성(Concurrency) 지원

**Concurrent Mode에서의 안전성**
React 18의 Concurrent Mode에서는 렌더링이 중단되고 재시작될 수 있습니다. 불변성이 보장되어야만 이러한 중단과 재시작이 안전하게 이루어집니다.

**멀티 스레드 환경에서의 안전성**
미래의 웹 워커나 멀티 스레드 환경에서 불변성은 데이터 경쟁(Race Condition)을 방지하는 핵심적인 메커니즘이 됩니다.

## 불변성의 심층적 구현 원리

### JavaScript 엔진 수준에서의 최적화

**Structural Sharing의 개념**
효율적인 불변성 구현을 위해 "구조적 공유"라는 개념이 사용됩니다. 변경되지 않은 부분은 이전 객체와 공유하고, 변경된 부분만 새로 생성하는 방식입니다.

**Copy-on-Write 메커니즘**
실제로 변경이 필요한 시점에만 복사를 수행하는 지연 복사 전략입니다. 이는 메모리 효율성과 성능을 동시에 확보합니다.

### 가비지 컬렉션과의 조화

**참조 체인의 정리**
불변성을 통해 더 이상 사용되지 않는 상태 객체들의 참조 체인이 명확해져, 가비지 컬렉션이 더 효율적으로 작동합니다.

**메모리 누수 방지**
직접 변경 방식에서 발생할 수 있는 순환 참조나 의도치 않은 참조 유지 문제를 불변성이 근본적으로 해결합니다.

## 핵심 개념: 불변성(Immutability)

React에서 state를 직접 변경하지 않고 `useState`나 `setState`를 사용해야 하는 이유는 **불변성(Immutability)** 원칙 때문입니다. React는 상태가 변경되었는지를 **참조 비교(Reference Equality)**로 판단하기 때문에, 기존 객체를 직접 수정하면 변경을 감지할 수 없습니다.

## 잘못된 방식 vs 올바른 방식

### 함수형 컴포넌트에서

#### 잘못된 방식 (직접 변경)

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  const [user, setUser] = useState({ name: 'John', age: 25 });
  const [items, setItems] = useState(['apple', 'banana']);

  // ❌ 잘못된 방식들
  const handleWrongIncrement = () => {
    count++; // 직접 변경 - 리렌더링 안됨
  };

  const handleWrongUserUpdate = () => {
    user.age = 26; // 객체 직접 변경 - 리렌더링 안됨
  };

  const handleWrongItemAdd = () => {
    items.push('orange'); // 배열 직접 변경 - 리렌더링 안됨
  };
}
```

#### 올바른 방식 (새로운 값/객체 생성)

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  const [user, setUser] = useState({ name: 'John', age: 25 });
  const [items, setItems] = useState(['apple', 'banana']);

  // ✅ 올바른 방식들
  const handleCorrectIncrement = () => {
    setCount(count + 1); // 새로운 값으로 업데이트
  };

  const handleCorrectUserUpdate = () => {
    setUser({ ...user, age: 26 }); // 새로운 객체 생성
  };

  const handleCorrectItemAdd = () => {
    setItems([...items, 'orange']); // 새로운 배열 생성
  };
}
```

### 클래스형 컴포넌트에서

#### 잘못된 방식

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
      user: { name: 'John', age: 25 },
      items: ['apple', 'banana'],
    };
  }

  // ❌ 잘못된 방식들
  handleWrongIncrement = () => {
    this.state.count++; // 직접 변경
    this.forceUpdate(); // 강제 렌더링 필요
  };

  handleWrongUserUpdate = () => {
    this.state.user.age = 26; // 객체 직접 변경
    this.setState({}); // 빈 객체로 강제 업데이트
  };
}
```

#### 올바른 방식

```jsx
class MyComponent extends React.Component {
  // ✅ 올바른 방식들
  handleCorrectIncrement = () => {
    this.setState({ count: this.state.count + 1 });
  };

  handleCorrectUserUpdate = () => {
    this.setState({
      user: { ...this.state.user, age: 26 },
    });
  };

  handleCorrectItemAdd = () => {
    this.setState({
      items: [...this.state.items, 'orange'],
    });
  };
}
```

## React가 변경을 감지하는 방식

### 참조 비교 (Reference Equality)

React는 성능상의 이유로 **얕은 비교(Shallow Comparison)**를 사용합니다.

```jsx
// React가 내부적으로 하는 비교
const prevState = { count: 1, user: { name: 'John' } };
const nextState = { count: 1, user: { name: 'John' } };

// 참조 비교 - 다른 객체이므로 변경으로 감지
console.log(prevState === nextState); // false

// 직접 변경의 경우
const state = { count: 1 };
const newState = state;
newState.count = 2;

console.log(state === newState); // true - 같은 참조!
// React는 변경을 감지하지 못함
```

### 깊은 비교의 성능 문제

```jsx
// 만약 React가 깊은 비교를 한다면...
function deepEqual(obj1, obj2) {
  // 모든 속성을 재귀적으로 비교
  // 매우 비용이 많이 드는 작업
  for (let key in obj1) {
    if (typeof obj1[key] === 'object') {
      if (!deepEqual(obj1[key], obj2[key])) {
        return false;
      }
    } else if (obj1[key] !== obj2[key]) {
      return false;
    }
  }
  return true;
}
```

이런 깊은 비교는 대규모 애플리케이션에서 성능 문제를 일으킬 수 있습니다.

## 불변성을 지키지 않으면 생기는 문제들

### 1. 리렌더링이 발생하지 않음

```jsx
function TodoList() {
  const [todos, setTodos] = useState([{ id: 1, text: '할일 1', completed: false }]);

  const toggleTodo = (id) => {
    // ❌ 잘못된 방식 - 리렌더링 안됨
    const todo = todos.find((t) => t.id === id);
    todo.completed = !todo.completed;
    setTodos(todos); // 같은 배열 참조
  };

  const toggleTodoCorrect = (id) => {
    // ✅ 올바른 방식 - 리렌더링 됨
    setTodos(todos.map((todo) => (todo.id === id ? { ...todo, completed: !todo.completed } : todo)));
  };
}
```

### 2. React DevTools에서 추적 불가

```jsx
// 직접 변경하면 React DevTools에서 상태 변화가 추적되지 않음
const handleBadUpdate = () => {
  user.name = 'Jane';
  setUser(user); // 같은 객체 참조
};
```

### 3. 최적화 기능 동작 안함

```jsx
// React.memo, useMemo, useCallback 등이 제대로 작동하지 않음
const MemoizedComponent = React.memo(function MyComponent({ data }) {
  return <div>{data.name}</div>;
});

// 부모 컴포넌트에서
const [userData, setUserData] = useState({ name: 'John', age: 25 });

const updateUser = () => {
  // ❌ 직접 변경 - MemoizedComponent가 리렌더링 안됨
  userData.name = 'Jane';
  setUserData(userData);
};
```

### 4. 시간 여행 디버깅 불가

```jsx
// Redux DevTools, React DevTools의 시간 여행 디버깅이 불가능
// 상태 히스토리가 올바르게 저장되지 않음
```

## 복잡한 상태의 불변성 관리

### 중첩 객체 업데이트

```jsx
function UserProfile() {
  const [user, setUser] = useState({
    personal: {
      name: 'John',
      age: 25,
    },
    preferences: {
      theme: 'dark',
      language: 'ko',
    },
  });

  // ❌ 잘못된 방식
  const updateNameWrong = (newName) => {
    user.personal.name = newName;
    setUser(user);
  };

  // ✅ 올바른 방식
  const updateNameCorrect = (newName) => {
    setUser({
      ...user,
      personal: {
        ...user.personal,
        name: newName,
      },
    });
  };

  // ✅ 더 간단한 방식 (immer 라이브러리 사용)
  const updateNameWithImmer = (newName) => {
    setUser(
      produce((draft) => {
        draft.personal.name = newName;
      })
    );
  };
}
```

### 배열 조작

```jsx
function TodoManager() {
  const [todos, setTodos] = useState([]);

  // ✅ 추가
  const addTodo = (text) => {
    setTodos([...todos, { id: Date.now(), text, completed: false }]);
  };

  // ✅ 삭제
  const deleteTodo = (id) => {
    setTodos(todos.filter((todo) => todo.id !== id));
  };

  // ✅ 수정
  const updateTodo = (id, newText) => {
    setTodos(todos.map((todo) => (todo.id === id ? { ...todo, text: newText } : todo)));
  };

  // ✅ 순서 변경
  const moveTodo = (fromIndex, toIndex) => {
    const newTodos = [...todos];
    const [movedTodo] = newTodos.splice(fromIndex, 1);
    newTodos.splice(toIndex, 0, movedTodo);
    setTodos(newTodos);
  };
}
```

## 성능 최적화와 불변성

### 1. React.memo와 불변성

```jsx
const ExpensiveComponent = React.memo(function ExpensiveComponent({ data }) {
  console.log('ExpensiveComponent 렌더링');

  return (
    <div>
      {data.items.map((item) => (
        <div key={item.id}>{item.name}</div>
      ))}
    </div>
  );
});

function Parent() {
  const [data, setData] = useState({ items: [] });

  const addItem = () => {
    // ❌ 직접 변경 - ExpensiveComponent가 리렌더링 안됨
    data.items.push({ id: Date.now(), name: 'New Item' });
    setData(data);

    // ✅ 불변성 유지 - ExpensiveComponent가 적절히 리렌더링됨
    setData({
      ...data,
      items: [...data.items, { id: Date.now(), name: 'New Item' }],
    });
  };

  return <ExpensiveComponent data={data} />;
}
```

### 2. useCallback과 불변성

```jsx
function ItemList({ items, onItemClick }) {
  // onItemClick이 변경될 때만 리렌더링
  return (
    <div>
      {items.map((item) => (
        <ExpensiveItem key={item.id} item={item} onClick={onItemClick} />
      ))}
    </div>
  );
}

function Parent() {
  const [items, setItems] = useState([]);
  const [filter, setFilter] = useState('');

  // ✅ 불변성을 지키면 useCallback이 제대로 작동
  const handleItemClick = useCallback(
    (id) => {
      setItems(items.map((item) => (item.id === id ? { ...item, selected: !item.selected } : item)));
    },
    [items]
  );

  return <ItemList items={items} onItemClick={handleItemClick} />;
}
```

## 불변성 유지를 위한 도구들

### 1. Immer 라이브러리

```jsx
import produce from 'immer';

function ComplexStateComponent() {
  const [state, setState] = useState({
    user: {
      profile: {
        name: 'John',
        settings: {
          theme: 'dark',
          notifications: true,
        },
      },
    },
    posts: [],
  });

  const updateUserName = (newName) => {
    // ✅ Immer를 사용하면 직관적으로 작성 가능
    setState(
      produce((draft) => {
        draft.user.profile.name = newName;
      })
    );
  };

  const addPost = (post) => {
    setState(
      produce((draft) => {
        draft.posts.push(post);
      })
    );
  };
}
```

### 2. 함수형 업데이트

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => {
    // ✅ 함수형 업데이트 - 이전 값을 기반으로 안전하게 업데이트
    setCount((prevCount) => prevCount + 1);
  };

  const incrementMultiple = () => {
    // 여러 번 호출해도 안전
    setCount((prev) => prev + 1);
    setCount((prev) => prev + 1);
    setCount((prev) => prev + 1);
  };
}
```

## 실제 개발에서의 모범 사례

### 1. 상태 구조 설계

```jsx
// ❌ 피해야 할 구조 - 너무 깊은 중첩
const badState = {
  app: {
    user: {
      profile: {
        personal: {
          details: {
            name: 'John',
          },
        },
      },
    },
  },
};

// ✅ 권장하는 구조 - 평면적 구조
const goodState = {
  user: { id: 1, name: 'John' },
  userProfile: { userId: 1, theme: 'dark' },
  userSettings: { userId: 1, notifications: true },
};
```

### 2. 커스텀 Hook으로 추상화

```jsx
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);

  const toggle = useCallback(() => {
    setValue((prev) => !prev);
  }, []);

  return [value, toggle];
}

function useList(initialList = []) {
  const [list, setList] = useState(initialList);

  const add = useCallback((item) => {
    setList((prev) => [...prev, item]);
  }, []);

  const remove = useCallback((index) => {
    setList((prev) => prev.filter((_, i) => i !== index));
  }, []);

  const update = useCallback((index, newItem) => {
    setList((prev) => prev.map((item, i) => (i === index ? newItem : item)));
  }, []);

  return { list, add, remove, update };
}
```

## 결론

React에서 state를 직접 변경하지 않는 이유는:

1. **변경 감지**: React가 참조 비교로 변경을 감지하기 때문
2. **성능 최적화**: 불변성을 통해 효율적인 리렌더링 가능
3. **디버깅**: 상태 변화 추적과 시간 여행 디버깅 지원
4. **예측 가능성**: 순수 함수처럼 동작하여 부작용 방지

**핵심 원칙:**

- 기존 객체/배열을 직접 수정하지 말고 새로운 객체/배열 생성
- 스프레드 연산자, `map`, `filter` 등 불변성을 유지하는 메서드 사용
- 복잡한 상태는 Immer 같은 라이브러리 활용 고려
- 함수형 업데이트 패턴 활용

이러한 불변성 원칙을 지키면 React 애플리케이션의 성능과 안정성을 크게 향상시킬 수 있습니다.
