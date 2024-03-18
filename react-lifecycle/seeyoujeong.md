## 리액트 라이프사이클이란?

리액트 컴포넌트가 생성되고 업데이트되며 제거되는 과정을 의미하며 마운트, 업데이트, 언마운트로 구성된다.

### 마운트

DOM이 생성되고 웹 브라우저상에 나타남을 의미하다.

### 업데이트

컴포넌트는 아래와 같은 경우에 업데이트된다.

- props가 바뀔 때
- state가 바뀔 때
- 부모 컴포넌트가 리렌더링될 때

### 언마운트

DOM에서 제거됨을 의미한다.

## 클래스형 컴포넌트의 라이프사이클 메서드

### render() 함수

이 메서드는 컴포넌트 모양새를 정의한다. 라이프사이클 메서드 중 유일한 필수 메서드이다.  
이 메서드 안에서 this.props와 this.state에 접근할 수 있으며, 리액트 요소를 반환한다.  
이 메서드 안에서는 이벤트 설정이 아닌 곳에서 setState를 사용하면 안되며, 브라우저의 DOM에 접근해서도 안된다.  
DOM 정보를 가져오거나 state에 변화를 줄 때는 componentDidMount에서 처리해야 한다.

```javascript
render() { ... }
```

### constructor 메서드

이 메서드는 컴포넌트의 생성자 메서드로 컴포넌트를 만들 때 처음으로 실행되고 초기 state를 정할 수 있다.

```javascript
constructor(props) { ... }
```

### getDerivedStateFromProps 메서드

props로 받아 온 값을 state에 동기화시키는 용도로 사용하며, 컴포넌트가 마운트될 때와 업데이트될 때 호출된다.

```javascript
static getDerivedStateFromProps(nextProps, prevState) { ... }
```

### componentDidMount 메서드

이 메서드는 컴포넌트를 만들고, 첫 렌더링을 다 마친 후 실행한다.  
이 안에서 라이브러리의 함수를 호출하거나 이벤트 등록, 비동기 작업을 처리하면 된다.

```javascript
componentDidMount() { ... }
```

### shouldComponentUpdate 메서드

이 메서드는 props 또는 state를 변경했을 때, 리렌더링을 시작할지 여부를 지정하는 메서드이다.  
이 메서드는 반드시 true 값 또는 false 값을 반환해야 한다. 기본적으로 언제나 true 값을 반환하지만 false 값을 반환하면 업데이트 과정은 여기서 중지된다.  
이 메서드 안에서 현재 props와 state는 this.props와 this.state로 접근하고, 새로 설정될 props 또는 state는 nextProps와 nextState로 접근할 수 있다.

```javascript
shouldComponentUpdate(nextProps, nextState) { ... }
```

### getSnapshotBeforeUpdate 메서드

이 메서드는 render에서 만들어진 결과물이 브라우저에 실제로 반영되기 직전에 호출된다.  
이 메서드에서 반환하는 값은 componentDidUpdate에서 세 번째 파라미터인 snapshot 값으로 전달받을 수 있고 주로 업데이트하기 직전의 값을 참고할 일이 있을 때 활용된다.

```javascript
getSnapshotBeforeUpdate(prevProps, prevState) { ... }
```

### componentDidUpdate 메서드

이 메서드는 리렌더링을 완료한 후 실행한다.  
업데이트가 끝난 직후이므로, DOM 관련 처리를 해도 무방하다.  
여기서는 prevProps 또는 prevState를 사용하여 컴포넌트가 이전에 가졌던 데이터에 접근할 수 있다.  
getSnapshotBeforeUpdate에서 반환한 값이 있다면 여기서 snapshot 값을 전달받을 수 있다.

```javascript
componentDidUpdate(prevProps, prevState, snapshot) { ... }
```

### componentWillUnmount 메서드

이 메서드는 컴포넌트를 DOM에서 제거할 때 실행한다.  
componentDidMount에서 등록한 이벤트, 타이머, 직접 생성한 DOM이 있다면 여기서 제거 작업을 해야 한다.

```javascript
componentWillUnmount() { ... }
```

### componentDidCatch 메서드

이 메서드는 컴포넌트 렌더링 도중에 에러가 발생했을 때 애플리케이션이 먹통이 되지 않고 오류 UI를 보여 줄 수 있게 해 준다.  
error는 파라미터에 어떤 에러가 발생했는지 알려 주며, info 파라미터는 어디에 있는 코드에서 요류가 발생했는지에 대한 정보를 준다.  
이 메서드를 사용할 때는 컴포넌트 자신에게 발생하는 에러를 잡아낼 수 없고 자신의 this.props.children으로 전달되는 컴포넌트에서 발생하는 에러만 잡아낼 수 있다.

```javascript
componentDidCatch(error, info) { ... }
```
