# 동기와 비동기란?

**동기**

동기는 프로세스를 순차적으로 처리하는 것을 의미한다. 여러 프로세스가 있다면 하나의 프로세스가 종료된 후에 다음 프로세스를 실행한다.

**비동기**

비동기는 프로세스를 병렬적으로 처리한다. 프로세스를 처리하고 있다면 백그라운드에서 다른 프로세스를 동시에 처리하는 방식이다.

**자바스크립트**

자바스크립트는 싱글 스레드로, 프로세스를 하나씩 순차적으로 처리한다. 하지만 자바스크립트에선 비동기 처리가 가능하다. 어떻게 그게 가능한 것일까?

# 자바스크립트의 비동기

자바스크립트의 비동기는 `런타임 환경` 을 통해 처리 가능하다. 런타임 환경이란 자바스크립트가 실행되는 환경을 의미한다. 이는 Node.js 혹은 웹 브라우저가 될 수 있다.

## 1. 자바스크립트 엔진

**Memory Heap**

참조 타입의 변수가 저장된다.

**Call Stack**

원시 타입의 변수가 저장된다.

코드를 실행할 때 필요한 환경 변수들을 모아놓은 **실행 컨텍스트**가 바로 여기에 쌓인다.

## 2. 이벤트 루프

**Web API**

이벤트 처리, 비동기 함수등은 브라우저에 위임된다. 보통 비동기 함수는 콜백함수를 가지고 있다. 비동기 처리가 끝난 콜백함수들은 Task Queue 에 쌓이게 된다.

**Task Queue**

이벤트 루프는 콜 스택이 비어있는지 확인하고, 태스크 큐에 쌓인 함수들은 하나씩 콜 스택에 넣어 처리한다.

# 기존의 문제

### 콜백지옥

자바스크립트의 비동기를 처리하기 위해 이전엔 콜백함수를 사용해 비동기를 처리했다.

아래는 `increase` 라는 함수를 사용해 1초 뒤 num 을 1 만큼 증가시키는 코드이다.

```jsx
const increase = (num) => {
	setTimeout(() => {
		num += 1;
	}, 1000);
};
```

만약 1초뒤 1이 되고, 2초 뒤에 2가 되고 3초 뒤에 3이 되는 등… 1초마다 num 이 1씩 증가하는 코드를 짜고 싶다면 어떻게 해야할까?

```jsx
const increase = (num) => {
	// 같은 코드
};

increase(1, (num) => {
	increase(num, (num) => {
		increase(num, (num) => {
			increase((num) => {
				//...
			});
		});
	});
});
```

위와 같이 콜백함수를 넣어주어 비동기함수를 처리해야한다.

### 이를 타개할 Promise

ES6 에선 비동기 작업을 제어하기 위한 새로운 문법으로 Promise 가 추가되었다. Promise 는 `then` 과 `catch` 를 사용하여 콜백함수가 아닌 메서드 체이닝으로 비동기를 처리할 수 있다.

# Promise

Promise 는 비동기 작업을 제어하기 위해 만든 문법으로써 콜백이 아닌 체이닝 기법으로 비동기를 처리할 수 있어 코드의 가독성을 높여준다.

### 프로미스 생성

프로미스는 비동기 처리 작업이 성공하면 `resolve` 객체를 사용해 성공값을 알려주고, 실패할 시 `reject` 객체를 사용해 실패를 알려준다.

```jsx
const promise = new Promise((resolve, reject) => {
	const data = fetch(URL);

	if (data) {
		resolve(data);
	} else {
		reject('데이터를 받아오는 도중 에러가 발생했어요!');
	}
});
```

### then 과 catch

`resolve` 와 `reject` 를 통해 넘겨준 인수는 각각 `then` 과 `catch` 를 사용해 처리할 수 있다.

```jsx
function testFunc() {
	return new Promise((resolve, reject) => {
		const data = fetch(URL);

		if (data) {
			resolve('성공!!');
		} else {
			reject('실패!!');
		}
	});
}
// 성공!!
textFunc()
	.then((result) => console.log(result)) // 성공!!
	.catch((error) => console.log(error)); // 실패!!
```

### finally

비동기 처리의 성공, 실패와 관계 없이 에러를 처리하기 위해 `finally` 를 사용할 수 있다.

```jsx
textFunc()
	.then((result) => console.log(result)) // 성공!!
	.catch((error) => console.log(error)) // 실패!!
	.finally(() => {
		console.log('난 할 일 다했다!');
	});
```

### 프로미스 체이닝

기존에 여러 비동기함수를 동기적으로 처리하기 위해선 콜백함수를 사용해 처리했기 때문에 가독성이 좋지 않았다.

```jsx
increase(1, (num) => {
	increase(num, (num) => {
		increase(num, (num) => {
			increase((num) => {
				//...
			});
		});
	});
});
```

하지만 프로미스를 사용하면 메서드 체이닝을 사용해 더 쉽게 동기적인 처리를 할 수 있다.

```jsx
increase(1)
	.then((value) => {
		return value + 1;
	})
	.then((value) => {
		return value + 1;
	})
	.then((value) => {
		return value + 1;
	})
	.then((value) => {
		console.log(value);
	});
```

# promise 내장함수

### all

promise.all() 메서드는 여러개의 프로미스를 배열로 받아 실행시키고 끝나면 then 을 실행시킨다.

```jsx
const promise1 = new Promise();
const promise2 = new Promise();

promise.all(promise1, promise2).then(result => { // ... });
```

### race

race 메서드는 여러개의 프로미스 중 하나라도 resolve 나 reject 가 되면 처리를 끝낸다.

```jsx
promise.race(promise1, promise2).then(result => { // ... });
```

### any

any 의 경우 여러개의 프로미스 중 하나라도 resolve 를 하면 처리를 끝낸다.

```jsx
promise.any(promise1, promise2).then(result => { // ... });
```

### setSettled

성공과 실패에 상관없이 promise 의 실행이 모두 끝난 경우 처리한다.

```jsx
promise.setSettled(promise1, promise2).then(result => { // ... });
```

# async & await

ES7 에서 새로 비동기 작업을 처리하기 위해 사용하는 새로운 문법이다.

### async

`async` 키워드는 비동기 작업을 동기적으로 처리하기 위해 함수 앞에 붙인다.

```jsx
async function name() {
	return 1;
}
```

`async` 키워드가 붙은 함수는 Promise 형태로 값을 반환한다. 따라서 위 함수는 다음 코드와 같이 동작한다.

```jsx
function name() {
	return new Promise(1);
}

name().then((value) => console.log(value)); // 1
```

### await

`async` 키워드가 붙은 함수 내에서만 동작하는 키워드다. `await` 가 붙은 비동기 작업은 해당 비동기 작업의 프로미스를 기다린 뒤 다음 코드를 실행한다.

```jsx
async function getData() {
	const data = await fetch(URL);

	if (data) {
		console.log(data);
	} else {
		console.log(error);
	}
}
```

### Promise 와의 비교

Promise 를 사용해서 비동기처리를 하기 위해선 `then` 과 `catch` 으로 처리해야했다.

async 와 await 를 사용하면 메서드 체이닝을 사용하지 않고 비동기 작업을 처리할 수 있기 때문에 가독성이 더 높아진다.

```jsx
// Promise 를 사용하는 경우
function getData() {
	return new Promise((resolve, reject) => {
		const data = fetch(URL);

		if (data) {
			resolve(data);
		} else {
			reject('error!');
		}
	});
}

getData()
	.then((data) => {
		console.log(data);
	})
	.catch((error) => {
		console.log(error);
	});
```

```jsx
// async & await 를 사용하는 경우
async function getData() {
	const data = await fetch(URL);

	if (data) {
		console.log(data);
	} else {
		console.log('error!');
	}
}
```
