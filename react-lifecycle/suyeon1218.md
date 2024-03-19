# React - 라이프 사이클

# 라이프 사이클

> 클래스 컴포넌트의 라이프 사이클과 함수 컴포넌트의 라이프 사이클에 대해서 알아보자.

# 클래스 형 컴포넌트

클래스형 컴포넌트에서는 컴포넌트의 생명주기를 직접 컨트롤 할 수 있는 다음과 같은 메서드들을 제공해주었다.

- 마운트 (Mount)
  - `componentWillMount` → (16.3v 이후) `constructor` `getDerivedStateFromProps`
  - 렌더 함수 호출
  - `componentDidMount`
- 업데이트
  - `componentWillReceiveProps` → (16.3v 이후) `getDerivedStateFromProps`
  - `shouldComponentUpdate`
  - 렌더 함수 호출
  - `componentWillUpdate` → (16.3v 이후) `getSnapshotBeforeUpdate`
  - `componentDidUpdate`
- 언마운트
  - `componentWillUnmount`

이제 각 키워드에 대한 정의를 하나씩 보며 라이프 사이클에 대해 이해해보자.

## Mount

- 16.3v 이전
  컴포넌트가 처음 실행되는 것을 의미한다. 컴포넌트가 처음 실행되면 context, defaultProps, state 를 지정하게 된다. 이후 `componentWillMount` 메서드를 호출한다.
  이후 render 로 컴포넌트를 DOM 에 연결하고, mount 가 완료되고 난 뒤에 `componentDidMount` 가 호출된다.

  > 📝 componentWillMount → componentDidMount

### 16.3v 이후

1. `constructor` 가 가장 먼저 실행된다. 컴포넌트에 필요한 state 및 초기값 등을 바인딩 하기 위해 사용한다.
2. `getDerivedStateFromProps` 메서드로 부모에게서 받은 prop 을 처음으로 초기화시킨다.
3. React 가 내부적으로 `render` 함수를 호출하여 처음 렌더링이 발생한다.
4. `componentDidMount` 메서드가 호출되고 렌더링이 완료된 뒤 외부 자원 요청 등의 처리를 한다.

마운트가 되고 있는 도중에는 props 나 state 를 바꿀 수 없고, DOM 이 렌더링 되기 이전의 시점이기 때문에 DOM 에도 접근할 수 없다.

## Update

prop 및 state 가 변경되어 컴포넌트가 업데이트 될 때의 과정이다.

- 16.3v 이전
  업데이트가 되기 전, 업데이트가 발생하는 것을 감지하고 `componentWillReceiveProps` 메서드가 호출되어 prop 을 업데이트 한다.
  이후 `shouldComponentUpdate`, `componentWilUpdate` 가 차례대로 호출된다. 각 메서드는 업데이트할 prop 에 대한 정보를 가지고 있다. `componentWillMount` 와 마찬가지로 `componentWillUpdate` 도중엔 state 를 바꿀 수 없다.
  업데이트가 완료되어 리렌더링이 되고 난 이후에 `componentDidUpdate` 가 실행된다.

  > 📝 props 이 업데이트 되는 경우 : componentWillReceiveProps → shouldComponentUpdate → componentWillUpdate → render → componentDidUpdate

  > 📝 state 가 업데이트 되는 경우 : shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate

### 16.3 v 이후

1. prop 이 변경된 경우엔 `getDerivedStateFromProps` 가 호출되어 변경된 prop 을 가지고 온다.
2. `componentShouldUpdate` 메서드가 호출되는데, 아직 렌더링이 일어나기 전이라 false 를 반환함으로써 렌더링을 취소할 수 있다. 주로 성능 최적화를 위해 사용한다.
3. React 에서 내부적으로 `render` 함수를 호출하여 리렌더링을 발생시킨다.
4. `getSnapShotBeforeUpdate` 를 통해 이전 DOM 의 정보를 가져올 수 있다. 해당 메서드의 반환값은 `componentDidUpdate` 의 세번째인자로 전달된다. 이를 통해 업데이트 전후의 차이를 계산할 수 있다. (스크롤 위치를 기억하는 등)

- `componentDidUpdate` 는 이전의 prop 정보와 새로운 prop 에 대한 정보를 둘 다 가지고 있다. 리렌더링이 완료된 다음 외부 자원 요청등의 처리를 할 수 있다.

## Unmount

컴포넌트가 제거되어 더 이상 사용하지 않을 때 발생하는 이벤트이다.

`componentWillUnmount` 가 실행된다.

# 함수형 컴포넌트

클래스형 컴포넌트는 라이프 사이클의 중심이 컴포넌트이다. 하지만 함수형 컴포넌트에선 특정 데이터에 대하여 라이프 사이클이 진행된다. 바로 `useEffect` 를 사용해서!

```jsx
useEffect(() => {
	console.log('userState is changed!');
}, [userState]);
```

`userState is changed!` 라는 문장은 컴포넌트가 첫 렌더링 될 때, 그리고 userState 가 변경될 때 마다 실행된다.

즉, `componentDidMount` 와 `componentDidUpdate` 가 합쳐진 셈이 된다. 또한 return 문을 사용하여 컴포넌트가 언마운트 될 때, (`componentWillUnmount`) 를 처리할 수도 있다.

```jsx
useEffect(() => {
	return () => {
		console.log('userState will change');
	};
}, [hidden]);
```

함수형 컴포넌트에서는 특정 데이터에 대해 라이프 사이클이 진행되므로, 데이터가 언마운트될 때 `userState will change` 라는 콘솔이 출력된다.

`componentWillMount` 와 `componentWillUpdate` 는 useEffect 에선 사용하지 않는다.

> ❓ 그렇다면 componentDidMount 와 componentDidUpdate 중 하나만 사용하고 싶다면요?

1. componentDidMount

   - 컴포넌트가 처음 마운트 되었을 때만을 체크하고 싶다면 다음과 같이 의존성 매개변수에 빈 배열을 넣어준다.

   ```jsx
   useEffect(() => {
   	console.log('component did Mount');
   }, []);
   ```

   - 데이터의 업데이트를 체크하고 싶지 않다면 `useRef` 를 사용하여 반응성은 가지고 있지 않지만, 렌더링이 되어도 초기화되지 않는 변수를 생성한다.

2. componentDidUpdate

   조건문과 `useRef` 를 사용하여 데이터가 업데이트 된 경우를 처리할 수 있다.
