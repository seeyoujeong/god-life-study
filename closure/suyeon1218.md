# 1. 클로저

---

결론부터 얘기하자면, 클로저란 어떤 함수 A 에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달할 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상을 의미한다.

<aside>
❗ 1. 클로저란, 함수를 선언할 때 만들어지는 유효범위가 사라진 이후에도 호출할 수 있는 함수이다. 
2. 클로저란, 이미 생명 주기가 끝난 외부 함수의 변수를 참조하는 내부 함수이다.

</aside>

### 1. 예시

outer 함수 내부에 inner 컨텍스트가 활성화된 시점에 inner는 outer의 렉시컬 환경에도 접근이 가능하다. 대게 outer 함수의 실행 컨텍스트가 종료되기 이전에 inner 함수의 실행 컨텍스트가 종료되며 이후 별도로 inner 함수를 호출할 수 없다.

> 그럼 outer 함수가 종료된 이후에도 inner 함수를 사용하려면 어떻게 해야할까?

inner 함수 자체를 반환값에 포함시켜줌으로써 해결할 수 있다.

```jsx
const outer = function () {
	let a = 1;
	const inner = function () {
		return ++a;
	};

	return inner; // inner 함수의 결과값이 아닌 inner 함수 자체를 반환
};

const outer2 = outer();
console.log(outer2()); // 2
```

그 이유는 가비지 컬렉터의 동작 방식 때문이다. 가비지 컬렉터는 어떤 값을 참조하는 변수가 하나라도 있으면 그 값은 수집 대상에 포함시키지 않는다. inner 함수는 outer2 를 실행함으로써 언젠가 호출될 가능성이 있으므로 가비지 컬렉터의 수집 대상에서 제외된다.

# 2. 메모리 관리

가비지 컬렉션에 수집 대상에서 제외되는 메모리들은 메모리 누수를 발생시킬 위험이 있으나, 현재는 거의 발견하기 힘들어졌으므로 의도대로 설계한 스코프에 대한 관리법을 알아야 한다.

```jsx
outer2 = null;
```

위의 예시에선 outer2가 inner 함수를 참조하고 있으므로 함수 참조를 끊는다.

```jsx
var a = 0;
var intervalid = null;
var inner = function () {
	if (++a >= 10) {
		clearInterval(intervalId);
		inner = null; // inner 식별자의 함수 참조를 끊는다.
	}
};
intervalId = setInterval(inner, 1000);
```

```jsx
var count = 0;
// ... 이벤트 추가 코드

var clickHandler = function () {
	if (count >= 10) {
		button.removeEventListener('click', clickHandler);
		clickHandler = null; // clickHandler 식별자의 함수 참조를 끊는다.
	}
};
```

setInterval이나 eventListenr 같은 비동기 함수의 경우 콜백 함수의 내부에서 식별자의 함수 참조를 끊는다. (각 코드는 어떤 함수 안에 있다고 가정)
