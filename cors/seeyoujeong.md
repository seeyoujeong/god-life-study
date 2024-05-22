# CORS

교차 출처 리소스 공유(Cross-Origin Resource Sharing)는 다른 출처의 리소스 공유에 대한 허용/비허용 정책이다.  
CORS를 통해 SOP 정책을 위반해도 다른 출처의 리소스를 사용할 수 있게 허용할 수 있다.

## SOP

SOP(Same-Origin Policy)는 동일한 출처에서만 리소스를 공유할 수 있는 정책이다.  
악의적인 경우를 방지하기위해 SOP가 생겨났다.  
동일한 출처인지 판단하는 기준은 출처(Origin)가 동일하면 된다.  
출처 비교와 차단은 브라우저의 역할이다.

> 출처(Origin)는 URL 중에 스킴 + 호스트 + 포트 부분을 의미한다.

## CORS 3가지 작동 방식

### 단순 요청 동작 과정

1. 클라이언트에서 HTTP 요청 헤더에 `Origin`을 담아서 보낸다.
2. 서버에서 응답 헤더에 `Access-Control-Allow-Origin`을 담아서 클라이언트로 전달한다.
3. 브라우저는 요청 헤더의 `Origin`값과 응답 헤더의 `Access-Control-Allow-Origin`값을 비교한다.
4. 만약 유효하지 않다면 CORS 에러를 내고 유효하다면 해당 리소스를 가져온다.

> 아래의 경우를 만족할 경우 단순 요청이 가능하다.
>
> 1. 요청 메소드는 `GET`, `HEAD`, `POST` 중 하나여야 한다.
> 2. `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, `DPR`, `Downlink, Save-Data`, `Viewport-Width`, `Width` 헤더만 포함되어야 한다.
> 3. `Content-Type` 헤더의 값은 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 중 하나여야 한다.

### 예비 요청 동작 과정

브라우저가 예비 요청(Preflight Request)를 OPTION 메소드를 통해 보내서 출처가 유효한지 확인한다.

1. 브라우저는 아래의 헤더들을 담아 예비 요청을 보낸다.
   - `Origin`
   - `Access-Control-Request-Method`
   - `Access-Control-Request-Headers`
2. 서버는 아래의 헤더들을 담아 브라우저로 보낸다.
   - `Access-Control-Allow-Origin`
   - `Access-Control-Allow-Method`
   - `Access-Control-Allow-Headers`
   - `Access-Control-Max-Age`
3. 브라우저는 요청과 응답을 비교해서 유효한지 검사한 후 유효하다면 요청을 보내고 아니면 에러를 낸다.

### 인증된 요청 동작 과정

인증된 요청은 클라이언트에서 서버에게 자격 인증 정보(세션ID가 포함된 쿠키, `Authorization`헤더에 설정하는 토큰 값)를 실어 요청할때 사용되는 요청이다.  
브라우저는 기본적으로 인증과 관련된 데이터를 요청에 담지 않는다. 이를 허용하려면 `credentials`옵션을 설정해야 한다.

1. 클라이언트에서 `credentails`옵션을 설정하고 요청을 시도하면 브라우저는 예비 요청을 보낸다.
2. 서버는 아래와 같은 조건을 충족해서 응답을 보낸다.
   - `Access-Control-Allow-Credentials`의 값을 true로 설정
   - `Access-Control-Allow-Origin`의 값에 와일드카드는 사용 불가
   - `Access-Control-Allow-Method`의 값에 와일드카드는 사용 불가
   - `Access-Control-Allow-Headers`의 값에 와일드카드는 사용 불가
3. 브라우저는 서버가 올바르게 조건을 만족했는지 확인하고 그렇지 않다면 응답을 거부하고 에러를 낸다.
