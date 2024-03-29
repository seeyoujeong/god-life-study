## 호이스팅이란?

실행 컨텍스트 생성 시, 해당 스코프내의 식별자가 스코프 최상단에 등록되는 동작 메커니즘을 의미한다.

## 변수 호이스팅

`var`, `let`, `const`로 변수를 선언하면 변수 호이스팅이 일어난다.  
`var`를 사용해 선언한 변수는 함수 스코프에 등록되며, 스코프가 시작될 때 `undefined`로 자동 초기화된다.  
`let`과 `const`를 사용해 선언한 변수는 블록 스코프에 등록되며, 스코프 시작시에 자동 초기화가 안되기 때문에 스코프 시작 지점부터 초기화 시작 지점까지 변수를 참조할 수 없는 구간이 생긴다.

## 함수 호이스팅

함수 선언문을 사용해 함수를 선언하면 함수 호이스팅이 일어난다.  
함수 선언문으로 함수를 선언하면 함수 이름에 해당하는 식별자가 스코프 최상단에 등록되고 함수 참조로 그 값이 자동으로 초기화된다.  
함수 호이스팅은 변수 호이스팅보다 우선순위가 높다.
