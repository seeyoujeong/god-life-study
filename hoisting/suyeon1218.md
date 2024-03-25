# 2. 호이스팅

---

## 1. LexicalEnvironment

LexicalEnvironment 의 내부는 다음으로 구성돼 있다.

- environmnetRecord
- outer-EnvironmentReference

## 2. environmentRecord

현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장된다. 함수 자체나 var로 선언된 변수의 식별자 등이 수집대상에 속한다. **컨텍스트 내부 전체를 처음부터 끝까지 쭉 훑어나가며 순서대로 수집한다.**

변수 정보를 수집하는 과정을 모두 마쳤더라도 코드는 아직 실행되기 전의 상태이다. 코드가 실행되기 전임에도 자바스크립트 엔진은 이미 해당 환경에 속한 코드의 변수명들을 모두 알고 있게 된다.

여기서 **실제로 식별자들을 최상단으로 끌어올리는 것은 아니지만, 마치 실제로 식별자들을 코드의 최상단에 끌어올린 것처럼 코드를 실행하게 된다.** 이를 호이스팅이라고 한다.

## 3. 오해

var 는 호이스팅 때문에 쓰지 않는 것을 권장한다고 한다. 하지만 실제론 let, const 도 호이스팅이 된다. 어떤 차이가 있는 걸까?

답은 `TDZ` 에 있다. Temporal Dead Zone 의 약자로, 시간상 사각지대라는 뜻이다. var 는 선언과 동시에 초기화가 undefined 로 이뤄지지만 let, const는 선언과 초기화가 따로 이뤄지기 때문에 참조 에러가 난다.

## 3. 예시

### 1. var 변수의 호이스팅

```jsx
function a(x) {
	console.log(x); // 1
	var x;
	console.log(x); // 2
	var x = 2;
	console.log(x); // 3
}

a(1);

/*
	호이스팅의 개념을 제외하고 본다면
	1 - 1
	2 - undefined
	3 - 2
	가 출력될 것으로 예상된다.
*/
```

```jsx
function a() {
	var x; // 매개변수는 모두 위로 끌어올려진 것처럼 동작하게 된다
	var x;
	var x;

	x = 1;
	console.log(x); // 1 - 1
	console.log(x); // 2 - 1
	x = 2;
	console.log(x); // 3 - 2
}

a(1);
```

### 2. 함수 호이스팅

```tsx
// 코드를 다음과 같이 작성하면 함수는 어떻게 호이스팅 되는가?

console.log(sum(1, 2));
console.log(multiply(3, 4));

function sum(a, b) {
	return a + b;
}

var multiply = function (a, b) {
	return a * b;
};
```

```tsx
// 다음과 같이 호이스팅된다.

var sum = function sum(a, b) {
	// 함수 선언문은 전체를 호이스팅 한다.
	return a + b;
};

var multiply; // 변수(함수 표현식)는 선언부만 호이스팅한다.

console.log(sum(1, 2));
console.log(multiply(3, 4)); // [ERROR] multiply is not a function

multiply = function (a, b) {
	// 할당부는 그 자리에 있다.
	return a * b;
};
```

## 4. 함수 선언식 & 함수 표현식

함수 선언식의 경우 같은 이름으로 선언한 경우 이후 선언한 함수가 이전의 함수를 덮어버리기 때문에 위험성이 높다. 하지만 함수 표현식으로 정의한다면 선언부만을 호이스팅하게 되므로 위험이 적다.

```jsx
// 함수 선언식의 경우

function sum(a, b) {
	return x + y;
}

console.log(sum(1, 2)); // 마지막에 선언된 sum 함수로 동작한다

function sum(a, b) {
	return 'x' + 'x' + 'y' + 'y';
}
```

```jsx
// 함수 표현식의 경우
var sum = function (a, b) {
	return x + y;
};

console.log(sum(1, 2)); // 이전에 선언된 함수로 출력

var sum = function (a, b) {
	return 'x' + 'x' + 'y' + 'y';
};

console.log(sum(1, 2)); // 이후 선언된 함수로 출력
```
