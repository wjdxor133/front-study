# CORS는 무엇인가


<img src="https://t1.daumcdn.net/thumb/R720x0/?fname=http://t1.daumcdn.net/brunch/service/user/1pJY/image/OhRUYAA_taKZtLVo5BMCpomOqUo.png" width="500" height="3000/">

 ## CORS란?
 ***Cross-Origin Resource Sharing(교차 출처 자원 공유)***
 ***도메인 또는 포트가 다른 서버의 자원을 요청하는 메커니즘***

다른 도메인의 img파일이나 css파일을 가져오는 것은 가능하나 <script/>로 감싸진 script에서 생성된 Cross-site HTTP Request는 Same-origin policy를 적용받아 Cross-site HTTP Request가 제한된다.

동일 출처로 요청을 보내는 것(Same-origin requests)은 항상 허용되지만 다른 출처로의 요청(Cross-origin requests)은 보안상의 이유로 제한된다는 뜻이다.



- 웹 애플리케이션은 리소스가 자신의 출처(도메인, 프로토콜, 포트)와 다를 때 교차 출처 HTTP 요청을 실행한다
- ***브라우저는 보안상의 이유로, 스크립트에서 시작한 교차 출처 HTTP요청을 제한한다***
ex) domain-a.com ↔domain-b.com 간의 요청은 CORS 정책 위반으로, 브라우저에서 요청을 제한
- 따라서 다른 출처의 리소스를 불러오기 위해서는, 그 출처에서 교차 출처 리소스 공유에 대한 헤더(CORS)를 응답시 반환 해주어야 한다.

## CORS는 왜 필요할까 ?

만약 내가 서비스하고 있지 않는 사이트에서 세션을 요청해서 세션을 획득할 수 있다면 해당 사이트는 악의적으로 내 세션을 탈취하거나 다른 행동을 할 수 있습니다. 그래서 브라우저에서는 이러한 요청을 막습니다
피싱사이트가 대표적인 공격 사례인데 이러한 것을 막고 내가 허용한 origin들만 요청 할 수 있도록 하기 위해 필요합니다

## CORS는 어떻게 동작하나

1. 기본적으로 웹은 다른 출처의 리소스를 요청할 때는 HTTP 프로토콜을 사용하여 요청을 하는데, 이때 브라우저는 요청 헤더(request header)에 Origin 필드에 요청을 보내는 출처를 담아 전송한다.

2. 서버는 요청에 대한 응답을 하는데, 응답 헤더 (response header)에 Access-Control-Allow-Origin이라는 값에 '이 리소스를 접근하는 것이 허용된 출처'를 내려준다.
이후 응답을 받은 브라우저는 자신이 보냈던 요청의 Origin과 서버가 보내준 응답의 Access-Control-Allow-Origin을 비교해 본 후 이 응답이 유효한 응답인지 아닌지를 결정한다.

> 위에가 기본적은 CORS동작의 흐름이다

## CORS 시나리오

### Preflight request

<img src="https://evan-moon.github.io/static/c86699252752391939dc68f8f9a860bf/6af66/cors-preflight.png" width="500" height="300"/>

W3C 명세에 의하면 브라우저는 먼저 서버에 Preflight request(예비 요청)를 전송하여 실제 요청을 보내는 것이 안전한지 OPTIONS method로 확인한다. 그리고 서버로부터 유효하다는 응답을 받으면 그 다음 HTTP request 메소드와 함께 Actual request(본 요청)을 보낸다. 만약 유효하지 않다면 에러를 발생시키고 실제 요청은 서버로 전송하지 않는다.

이러한 예비 요청과 본 요청에 대한 서버의 응답은 프로그래머가 구분지어 처리하는 것은 아니다. 프로그래머가 Access-Control- 계열의 Response Header만 적절히 정해주면 Options 요청으로 오는 예비 요청과 GET, POST, PUT, DELETE 등으로 오는 본 요청의 처리는 서버가 알아서 처리한다. 

Preflight request는 GET, HEAD, POST외 다른 방식으로도 요청을 보낼 수 있고 application/xml처럼 다른 Content-type으로 요청을 보낼 수도 있으며, 커스텀 헤더도 사용할 수 있다.


### Simple Request

<img src="https://evan-moon.github.io/static/d8ed6519e305c807c687032ff61240f8/6af66/simple-request.png" width="500" height="300"/>

Simple Request란 아래 3가지 조전을 만족하는 경우를 말한다.

- GET / HEAD / POST 중 한 가지 메소드를 사용해야 한다.
- User agent에 의해 자동으로 설정되는 헤더(Connection, User-Agent 또는 Fetch spec에서 '금지된 헤더 이름(forbidden header name)'으로 정의된 이름들을 가진 헤더들)을 제외하고, Fetch spec에서 'CORS-safelisted request-header'라고 정의되어있고 수동 설정이 허용된 헤더들은 다음과 같다.
  - Accept
  - Accept-Language
  - Content-Language
  - Content-Type
  - DPR
  - Downlink
  - Save-Data
  - Viewport-Width
  - Width
- 오직 아래의 Content Type만 지정해야 한다
  - application / x-www-form-urlencoded
  - multipart/form-data
  - text/plain


### Credentialed Request
 
HTTP Cookie와 HTTP Authentication 정보를 인식할 수 있게 해주는 요청이다. 기본적으로 브라우저는 Non credential로 설정되어있기 때문에 credentials 전송을 위해선 설정을 해주어야 한다. 

Simple Credential Request 요청 시에는 xhr.withCredentials = true를 지정해서 Credential 요청을 보낼 수 있고, 서버는 Response Header에 반드시 Access-Control-Allow-Credentials: true를 포함해야한다.

또한 서버는 credential 요청에 응답할 때 반드시 Access-Control-Allow-Origin 헤더의 값으로 "*"와 같은 와일드카드 대신 ***구체적인 도메인***을 명시해야한다.

## CORS를 해결할 수 있는 방법

### Access-Control-Allow-Origin 세팅하기

CORS 정책 위반으로 인한 문제를 해결하는 가장 대표적인 방법은, 정석대로 서버에서 Access-Control-Allow-Origin헤더에 알맞은 값을 세팅해주는 것이다.

이때 와일드카드인 *을 사용하여 이 헤더를 세팅하게 되면 모든 출처에서 오는 요청을 받아먹겠다는 의미이므로 당장은 편할 수 있겠지만, 바꿔서 생각하면 정체도 모르는 이상한 출처에서 오는 요청까지 모두 받아먹겠다는 오픈 마인드와 다를 것 없으므로 보안적으로 심각한 이슈가 발생할 수도 있다.

그러니 가급적이면 귀찮더라도 Access-Control-Allow-Origin: https://evan.github.io와 같이 출처를 명시해주도록 하자.

### Webpack Dev Server로 리버스 프록싱하기

프론트엔드 개발자는 대부분 웹팩과 webpack-dev-server를 사용하여 자신의 머신에 개발 환경을 구축하게 되는데, 이 라이브러리가 제공하는 프록시 기능을 사용하면 아주 편하게 CORS 정책을 우회할 수 있다.

```javascript
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'https://api.evan.com',
        changeOrigin: true,
        pathRewrite: { '^/api': '' },
      },
    }
  }
}
```

이렇게 설정을 해놓으면 로컬 환경에서 /api로 시작하는 URL로 보내는 요청에 대해 브라우저는 localhost:8000/api로 요청을 보낸 것으로 알고 있지만, 사실 뒤에서 웹팩이 https://api.evan.com으로 요청을 프록싱해주기 때문에 마치 CORS 정책을 지킨 것처럼 브라우저를 속이면서도 우리는 원하는 서버와 자유롭게 통신을 할 수 있다. 즉, 프록싱을 통해 CORS 정책을 우회할 수 있는 것이다.


다만 이 방법은 실제 프로덕션 환경에서도 클라이언트 어플리케이션의 소스를 서빙하는 출처와 API 서버의 출처가 같은 경우에 사용하는 것이 좋다. 물론 로컬 개발 환경에서야 웹팩이 요청을 프록싱해주니 아무 이상이 없겠지만, 어플리케이션을 빌드하고 서버에 올리고 나면 더 이상 webpack-dev-server가 구동하는 환경이 아니기 때문에 프록싱이고 나발이고 이상한 곳으로 API 요청을 보내기 때문이다.

출처 
[https://velog.io/@pilyeooong/CORS%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80](https://velog.io/@pilyeooong/CORS%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
[https://evan-moon.github.io/2020/05/21/about-cors/](https://evan-moon.github.io/2020/05/21/about-cors/)