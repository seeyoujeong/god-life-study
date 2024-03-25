# Hoisting 이란?

인터프리터가 코드를 실행하기 전에 함수, 변수, 클래스 또는 import 의 선언문을 해당 스코프의 맨 위로 끌어올리는 것 처럼 보이는 현상

JS엔진은 `컴파일 ⇒ 인터프리팅` 순서로 코드를 처리하는데, 컴파일 단계에서 선언과 초기화를 분리하여 인식한다.

<br>

## 예시 1

```jsx
a = 20;
let a;
console.log(a);
```

- 선언 : `let a` / 초기화 : `a = 20`
- 결과 : `ReferenceError : Cannot access ‘a’ before initialization`

<br>

## 예시 2

```jsx
b = 30;
var b;
console.log(b);
```

- 선언 : `var b` / 초기화 : `b = 30`
- 결과 : `30`

<br>

## 예시1 에서만 에러가 발생하는 이유

두 예시의 차이는 **변수 선언 키워드**의 차이이다.
**예시1**에서는 `let` 을, **예시2**에서는 `var` 를 사용하여 변수를 선언해주었다.

> ### var 를 통한 변수 선언

var 키워드로 변수를 선언하면, 호이스팅으로 선언부가 스코프의 제일 위로 끌어올려지더라도 초기화가 동시에 이루어진다.

예시2에서 정확히는 아래와 같은 일이 발생한다.

```jsx
var b = undefined; // 호이스팅된 선언 + 초기화
b = 30;
```

따라서 콘솔을 이렇게 찍어보면,

```jsx
console.log(b);
b = 30;
var b;
```

`undefined` 가 출력되는 것이다.

> ### let, const 를 통한 변수 선언

let 또는 const 로 변수를 선언하면, **선언과 초기화가 분리**되어 일어나며 **선언 이전에 변수를 사용하는 것이 엄격하게 금지**된다. 이는 **TDZ**(Temporal Dead Zone)의 영향 때문이다.

<br>

## TDZ 란?

변수 스코프의 맨 위(선언단계)에서 변수의 초기화 완료 시점까지의 변수를 **시간상 사각지대 (Temporal Dead Zone, TDZ)** 에 들어간 변수라고 한다.

```jsx
{
  // TDZ가 스코프 맨 위에서부터 시작
  // = 호이스팅으로 인한 선언 단계

  const printNumber = () => {
    console.log(number);
  };

  console.log(number);
  // TDZ 안에서 number에 접근하면 ReferenceError

  let number = 100;
  // 이 코드가 실행되었을 때(= 초기화) number의 TDZ 종료

  func(); // TDZ 끝나고 호출 : 100
}
```

시간상 사각지대에서 변수를 사용하려 한다면 `ReferenceError` 가 발생한다.

```jsx
a = 2; // ReferenceError!
let a;
```

다만, 선언 구문에 초기 값을 지정하지 않은 경우는 `undefined` 로 초기화 된다.

```jsx
let a; // a : undefined
a = 2; // a : 2
```

<br>

## 함수의 호이스팅

함수의 경우, 어떻게 작성하느냐에 따라 달라진다.

### 함수 선언식

> 함수 이름과 함께 함수를 선언하는 방식

함수 선언 역시 호이스팅되기 때문에, 함수를 선언하기 전에 호출할 수 있다. (유효한 스코프 내에서)

```jsx
sayHello(); // 출력 : Hello!

function sayHello() {
  console.log('Hello!');
}
```

코드 상에서 선언하기 이전에 호출해도 정상 작동한다.
함수 선언이 호이스팅되어 코드가 실행되기 전 스코프의 최상단으로 끌어올려졌기 때문이다.

### 함수 표현식

> 변수에 함수를 할당하는 방식

함수 표현식은 말 그대로 변수에 함수를 할당하는 것이다.

```jsx
sayHello();
// ReferenceError: Cannot access 'sayHello' before initialization

let sayHello = () => {
  console.log('Hello!');
};
```

⬆️ 이 예시의 경우, sayHello 를 호출하면 `ReferenceError` 가 발생한다. 스코프의 최상단에서 sayHello 의 선언되었지만, 아직 초기화되지는 않았기 때문이다.

```jsx
sayHello();
// TypeError: sayHello is not a function

var sayHello = () => {
  console.log('Hello!');
};
```

⬆️ var 키워드로 변수를 선언하고 함수를 할당한다면 위와는 달리 `TypeError` 가 발생한다. sayHello 변수의 선언은 호이스팅되었지만, **할당된 함수는 호이스팅되지 않기 때문이다.**

정확히는 저기서 `undefined` 를 호출한 것이다. **함수가 아닌 것을 함수처럼 호출**했기 때문에 타입에러가 발생했다.
