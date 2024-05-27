## 버블링

버블링은 특정 요소에서 이벤트가 발생하면, 해당 요소에 할당된 핸들러부터 가장 최상단의 조상 요소에 할당된 핸들러까지 거슬러 올라가면서 동작하는 과정을 말한다.  
`addEventListener`의 세번째 인수가 없거나 `false`, `{ capture: false }`인 핸들러만 버블링때 동작한다.  
버블링을 중단하는 방법은 `event.stopPropagation()`을 사용하면 된다.

## 캡처링

캡처링은 버블링과 반대되는 과정으로 가장 최상단의 조상 요소부터 이벤트가 발생한 요소까지 내려가며 할당된 핸들러를 동작하는 과정을 말한다.  
`addEventListener`의 세번째 인수가 `true`이거나 `{ capture: true }`인 핸들러만 캡처링때 동작한다.

## 버블링과 캡처링이 발생하지 않는 이벤트 목록

1. focus
2. blur
3. load
4. unload
5. mouseenter
6. mouseleave
7. pointerenter
8. pointerleave
9. beforeunload

## 이벤트 위임

이벤트 위임은 여러 개의 하위 요소에 핸들러를 할당하는 대신 하나의 상위 요소에 핸들러를 할당하는 방법을 말한다.  
많은 핸들러를 할당하지 않아도 되기 때문에 초기화가 단순해지고 메모가 절약된다.
