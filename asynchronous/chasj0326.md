## 동기/비동기

> 요청과 동시에 결과를 만들어야 하는가?
> 
- 동기 Synchronous
    - 요청이 주어짐과 **동시에 결과 내놓기**
    - 이전 요청이 결과를 내놓기 전 까지, 다음 요청을 실행하지 않음
    - 멀티 불가능
- 비동기 Asynchronous
    - 요청과 결과는 **동시에 일어나지 않음**
    - 이전 요청이 결과를 만들지 못했어도, 다음 요청 실행 가능
    - 멀티 태스킹

## 블로킹/논블로킹

> 작업이 차단+대기 되느냐 아니냐
> 
- 블로킹 : 하나의 작업이 전체의 흐름을 **막음**
- 논블로킹 : 작업은 전체의 흐름을 **막지 않음**. 도중에 다른 작업 가능

## 자바스크립트와 동기/비동기

비동기로 작업을 요청하면 브라우저에 내장된 멀티 스레드로 이루어진 Web API에 작업이 인가되어 메인 Call Stack과 작업이 동시에 처리된다. 

자바스크립트 실행 코드 자체는 Call Stack에서 실행된다! 
하지만 애니메이션 실행, 네트워크 통신, 마우스 키보드 입력, 타이머 등등의 비동기 작업들은 Web API(멀티스레드) 에게 맡겨버리기

- `Call Stack` : 자바스크립트 엔진이 **코드 실행을 위해** 사용하는 메모리 구조
- `Heap` : 동적으로 생성된 자바스크립트 객체가 저장되는 공간
- `Web APIs` : 브라우저에서 제공하는 API 모음으로, 비동기적으로 실행되는 작업들을 전담 (데이터 패칭, 타이머, DOM 조작)
- `Callback Queue` : 비동기 작업이 완료되면 실행되는 함수가 대기하는 공간
- `Event Loop` : 비동기 함수들을 적절한 시점에 실행(콜 스택으로 넘기기)시키는 관리자

**자바스크립트가 비동기를 처리하는 방식**

→ 서버와의 네트워크 통신이나, 타이머와 같은 Web API 기능들은 브라우저에서 멀티스레드 방식으로 동시 처리될 수 있다. 이 후 안에 있는 실행 코드들은 테스크 큐에 추가되고, 이벤트 루프에 의해 콜 스택(싱글 스레드)에 들어가 최종적으로 실행된다.

- 마이크로테스크 큐
    - Promise의 then(), catch(), Promise.resolve(), Promise.reject() 의 콜백함수 (프로미스 핸들러)
    - 우선순위가 더 높다
- 매크로테스크 큐
    - 타이머 API 콜백 함수

## 콜백 함수

비동기 함수의 매개변수에 실행할 함수를 넘겨, 비동기 함수 내부에서 매개변수 함수를 실행하는 방법이다.

**단점 :** 흐름을 추적하기 어려우며, 비동기 실행 후 순차적으로 실행되어야 하는 코드의 양이 증가하면 콜백 함수가 복잡하게 중첩될 수 있다. 이를 콜백 지옥이라고 한다.

## Promise

콜백함수를 통한 비동기 처리의 단점을 보완하기 위해 나온 문법이다. 비동기 작업의 성공(fulfilled), 실패(rejected), 대기(pending) 상태를 나타내는 비동기 처리 전용 객체이다. 비동기 작업 함수가 Promise 객체를 반환하면, then 핸들러를 통한 프로미스 체이닝으로 이 값을 사용할 수 있다.

**단점 :** 비동기 작업의 복잡도가 증가하면, 끝없이 프로미스 체이닝을 작성해야 하는 문제점이 있다. 이걸 Promise Hell 이라고 부르기도 한다. 프로미스를 반환하는 비동기 함수의 결과 값은 계속 then 을 통해서 가공할 수 있는 것이다. 

## async / await

Promise 의 단점을 보완하기 위해 나온 문법이다. 프로미스를 기반으로 똑같이 작동하지만, 콜백이나 체이닝을 통해서가 아닌, 비동기 결과를 값으로서 동기 코드처럼 작성할 수 있도록 해준다. 비동기 흐름을 추적하기 쉬워진다.

- `async` : 프로미스를 반환하는 비동기 함수의 선언부나 표현식 앞에 사용된다.
- `await` : async 함수 내부에서 사용된다. Promise 객체 앞에 사용되어 resolve 될 때 까지 이후 코드들을 일시중지 시킨다.