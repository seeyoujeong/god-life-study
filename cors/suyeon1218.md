# CORS 에러

CORS 에러를 풀어 쓰면 Cross Origin Resource Sharing 정책이다. 교차 출처 리소스 정책이라고 얘기하는 이 에러는 무엇일까?

그건 바로 동일한 출처에 대해서만 리소스를 공유할 수 있는 Same Origin Policy(SOP) 정책을 해결할 수 있게 해주는 정책이다. 그렇다면 SOP 는 무엇이고, 이걸 어떻게 해결한다는 걸까?

### Same Origin Policy

SOP 정책은 같은 출처(Origin)끼리만 리소스를 공유할 수 있다는 정책이다. 즉 Origin 이 같으면 자유롭게 리소스를 교환할 수 있으나 Origin 이 다른 경우엔 제한이 걸린다.

### Origin?

Origin 이 같은지를 판별하기 위해선 URL 을 뜯어봐야 한다. 예를 들어 아래와 같은 URL 이 있다고 가정하자.

```
https://www.examples.com:3000
```

해당 URL 을 나누게 되면 다음 요소로 나뉜다.

- https:// - Protocol
- www.examples.com - Host
- :3000 - Port

Origin 을 검사할 때 브라우저는 Protocol, Host, Port 이 세가지가 일치하면 동일한 출처로 판단한다.

### 다시보는 Cross Origin Resource Sharing(CORS)

CORS 를 대체로 CORS 에러! 로 묶기 때문에 하나의 에러로 취급할 수 있지만, 실은 다른 출처의 Origin 과도 리소스를 공유할 수 있도록 하는 정책이다.

즉, SOP 정책을 위반하더라도 CORS 정책에 부합하면 다른 출처의 리소스도 허용한다는 뜻이 된다.

### CORS 기본 동작

1. 클라이언트가 HTTP 통신 헤더에 Origin 을 담아서 보낸다.
2. 서버는 응답 헤더에 Access-Control-Allow-Origin 을 담아 클라이언트로 전달한다. 해당 값에는 서버가 허용한 Origin 목록들이 정의되어 있다.
3. 클라이언트가 Origin 과 서버가 보내준 Access-Control-Allow-Origin 을 비교한다.

→ 즉 서버가 Access-Control-Aloow-Origin 에 클라이언트의 Origin 을 등록해 줌으로써 해결할 수 있다.
