# 이벤트 전파 흐름

1. 이벤트가 하위 요소로 전파된다.
2. 이벤트가 실제 타깃 요소에 도달해 이벤트 리스너를 실행한다.
3. 이벤트가 상위 요소로 전파된다.

# 이벤트 버블링

자식 요소에서 발생한 이벤트가 부모 요소로 전파되는 것을 의미한다. 브라우저의 이벤트는 기본적으로 버블링으로 이뤄진다.

```tsx
const handleClick = (event) => {
  console.log(event.target.className);
};
```

```tsx
<div
  onClick={handleClick}
  className='one'>
  <div
    onClick={handleClick}
    className='two'>
    <div
      onClick={handleClick}
      className='three'></div>
  </div>
</div>
```

위 코드에서 `three` 클래스이름이 적힌 요소를 클릭하게 되면 다음과 같이 콘솔이 출력된다.

```
three
two
one
```

# 이벤트 캡처링

이벤트 버블링과 반대로 이벤트 전파가 부모요소에서 자식요소로 이뤄지는 것을 말한다.

이벤트 캡처링을 위해선 별도의 자바스크립트 설정이 필요하다.

이벤트 리스너의 세번째 매개변수에 캡쳐 속성을 true 로 설정하면 이벤트 캡처링이 발생하고, false (default) 로 설정하면 이벤트 버블링이 발생한다.

```jsx
element.addEventListener('click', handleClick, { capture: true });
element.addEventListener('click', handleClick, true);
```

# 이벤트 전파 막기

### event.stopPropagation()

이벤트 전파를 막기 위해서 사용한다.

```jsx
child.addEventListener('click', (event) => {
  event.stopPropagation();
});
```

### event.stopImmediatePropagation()

한 요소에 등록된 이벤트가 여러개인 경우 어떠한 조건에서 하나의 이벤트만 실행시키고 싶을 때 사용한다.

```jsx
child.addEventListener('click', (event) => {
  if (어떤_조건) {
    event.stopImmediatePropagation();
  }
});

child.addEventListener('click', (event) => {
  // ...
});
```

# 이벤트 위임

이벤트 버블링, 캡처링을 이용하여 이벤트 위임이라는 핸들링 패턴을 사용할 수 있다. 해당 방법을 이용하면 요소마다 이벤트를 할당해주지 않고, 부모 요소에서 이벤트를 할당하여 관리할 수 있다.

기존에는 각 엘리먼트에 다음과 같이 이벤트를 하나씩 추가해주었다.

```jsx
nodes.map((node) => {
  node.addEventListener('click', clickNodeEvent);
});
```

이벤트를 컴포넌트 자체에 걸고, 이벤트 버블링을 사용해서 이벤트 탐색을 한다.

이벤트 발생 시 발생 지점으로부터 부모 노드까지 이벤트가 전파되기 때문에 이벤트 버블링을 사용하면 컴포넌트에 일일이 사용하지 않는 이벤트를 넣어줄 필요가 없게 된다.

```jsx
$form.addEventListener('click', (event) => {
  const $li = e.target.closest('li'); // 이벤트와 가장 가까운 해당 노드를 찾는다
  const { index } = $li.dataset;

  if ($li !== null) {
    if (event.target.className === 'remove') {
      this.onRemove();
    } else {
      this.onToggle();
    }
  }
});
```
