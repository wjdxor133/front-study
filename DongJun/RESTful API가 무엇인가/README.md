# RESTful API가 무엇인가

## REST API란?
- HTTP 프로토콜 장점을 살릴 수 있는 네트워크 기반 아키텍쳐
- REST API를 구현하기 위해서는 HTTP method + 모든 개체 Resource화 + URL디자인(라우팅) 필요하다
 **라우팅이란** 
 클라이언트의 요쳉에 대한 결과를 어떻게 이어줄 것인가를 처리하는 것

 ### REST API 설계 규칙
1. 슬래시 구분자(/ )는 계층 관계를 나타내는데 사용한다.
    Ex) http://restapi.example.com/houses/apartments
2. URI 마지막 문자로 슬래시(/ )를 포함하지 않는다.
    URI에 포함되는 모든 글자는 리소스의 유일한 식별자로 사용되어야 하며 URI가 다르다는 것은 리소스가 다르다는 것이고, 역으로 리소스가 다르면 URI도 달라져야 한다.
    REST API는 분명한 URI를 만들어 통신을 해야 하기 때문에 혼동을 주지 않도록 URI 경로의 마지막에는 슬래시(/)를 사용하지 않는다.
    Ex) http://restapi.example.com/houses/apartments/ (X)
3. 하이픈(- )은 URI 가독성을 높이는데 사용
    불가피하게 긴 URI경로를 사용하게 된다면 하이픈을 사용해 가독성을 높인다.
4. 밑줄(_ )은 URI에 사용하지 않는다.
    밑줄은 보기 어렵거나 밑줄 때문에 문자가 가려지기도 하므로 가독성을 위해 밑줄은 사용하지 않는다.
5. URI 경로에는 소문자가 적합하다.
    URI 경로에 대문자 사용은 피하도록 한다.
    RFC 3986(URI 문법 형식)은 URI 스키마와 호스트를 제외하고는 대소문자를 구별하도록 규정하기 때문
6. 파일확장자는 URI에 포함하지 않는다.
    REST API에서는 메시지 바디 내용의 포맷을 나타내기 위한 파일 확장자를 URI 안에 포함시키지 않는다.
    Accept header를 사용한다.
    Ex) http://restapi.example.com/members/soccer/345/photo.jpg (X)
    Ex) GET / members/soccer/345/photo HTTP/1.1 Host: restapi.example.com Accept: image/jpg (O)
7. 리소스 간에는 연관 관계가 있는 경우
    /리소스명/리소스 ID/관계가 있는 다른 리소스명
    Ex) GET : /users/{userid}/devices (일반적으로 소유 ‘has’의 관계를 표현할 때)
    https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html


## REST란
<img src="https://gmlwjd9405.github.io/images/network/rest.png"  width="500" height="300">

- REST의 정의 
  - "Representational State Transfer"의 약자
  - 일반적인 웹을 만들기 위한 제약 조건들을 나열하여 제안한 일종의 디자인 가이드이다.
  - 자원을 이름(자원의 표현)으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것을 의미한다
  - 어떤 자원에 대해 CRUD(Create, Read, Update, Delete) 연산을 수행하기 위해 URL(Resource)로 보내는 것

## 왜 REST를 사용할까 ?

애플리케이션의 복잡도가 증가하면서 애플리케이션을 어떻게 분리하고 통합하느냐가 주요 이슈가 되었고,
이에 자바 진영에서는 과거 EJB(Enterprise Java Beans), SOA(Service Oriented Architecture)에 이어 최근에는 MSA(Micro Service Architecture)와 함께
REST가 떠올랐다.

그리고 모바일같은 다양한 클라이언트가 등장하면서 Backend 하나로 다양한 Device를 대응하기 위해 REST의 필요성이 증대되었다

## REST의 특징
1. Uniform (유니폼 인터페이스) : HTTP 표준에만 따른다면, 안드로이드/IOS플랫폼이든, 특정 언어나 기술에 종속 되지 않고 모든 플랫폼에 사용이 가능하며,
URl로 지정한 리소스에 대한 조작이 가능한 아키텍처 스타일을 의미한다.

2. Stateless(무상태성) : HTTP는 Stateless Protocol이므로, REST역시 무상태성을 갖는다. 즉 HttpSession과 같은 컨텍스트 저장소에 상태정보를 따로 저장하고
관리하지 않고, API서버는 들어오는 요청만을 단순 처리하면 된다. 세션과 같은 컨텍스트 정보를 신경쓸 필요가 없어 구현이 단순해진다.

3. Cacheable(캐쉬가능) : HTTP 기준의 웹 표준을 그대로 사용하므로, 웹에서 사용하는 기존의 인프라를 그대로 활용가능하다.
HTTP가 가진 가장 강력한 특징중의 하나인 캐싱 기능을 적용할 수 있다.

4. Self0descriptiveness(자체 표현 구조) : 동사(Method) + 명사(URl)로 이루어져있어 어떤 메서드에 무슨 행위를 하는지 알 수 있으며,
메시지 포맷 역시 JSON을 이용해서 직관적으로 이해가 가능한 구조로, REST API 메시지만 보고도 이를 쉽게 이해할 수 있다.

5. Client-Server 구조 : REST 서버는 API 제공, 클라이언트 사용자 인증이나 컨텍스트(세션, 로그인 정보 등)을 직접 관리하는 구조로
각각의 역할이 확실히 구분되기 때문에 클라이언트와 서버에서 개발해야 할 내용이 명확해지고 서로간 의존성이 줄어들게 된다.

6. Layered System(계층형 구조) : API 서버는 순수 비지니스 로직을 수행하고, 그 앞단에 사용자 인증, 암호화, 로드밸런싱 등을 하는 계층을 추가하여 구조상의 유연상을 둘 수 있다.

## REST의 구성
- REST 요소를 파악하는데는 Richardson Maturity Moder(RMM)을 기준으로 이해하는 것이 좋은 것 같다
<img src="https://t1.daumcdn.net/cfile/tistory/99480C4A5BD6CCD31C" width="500" heigth="300" >

### LEVEL 0

<img src="https://t1.daumcdn.net/cfile/tistory/99BE8B4A5BD6CCEB15" width="500" heigth="300">

웹의 기본 메커니즘을 전혀 사용하지 않는 단계로, HTTP를 통해 데이터를 주고받지만 모든 데이터 요청 및 응답과 관련한 정보를 HTTP의 body 정보를 활용한다. POX(Plain Old XML)로 요청과 응답을 주고받는 RPC 스타일 시스템이다. 이 때 HTTP method는 POST만 사용하며, 특정 서비스를 위해 클라이언트에서 접근할 endpoint는 하나이다.

즉, 하나의 URI을 가지고, 하나의 HTTP method(mainly POST)를 사용한다. 내용에 대한 구분은 XML을 Payload로 사용해서 요청을 구분하는 방식을 취한다.  모든걸 하나의 리소스를 가지고 처리한다.

### LEVEL 1 RESOURCE

<img src="https://t1.daumcdn.net/cfile/tistory/998F12385BD6CCFF1B" width="500" heigth="300">

RMM에서 REST를 위한 첫 단계는 리소스를 도입하는 것이다. 그래서 이제는 모든 요청을 단일 서비스 endpoint로 보내는 것이 아니라, 개별 리소스와 통신한다.
```
POST /doctors/mjones HTTP/1.1
​[various other headers]
​<openSlotRequest date = "2010-01-04"/>
```
즉, 함수를 호출하고 인자들을 넘기는 것이 아니라 다른 정보를 위해 인자들을 제공하는 특정 리소스로 요청을 보낸다. 이럴 경우의 이점은 자원이 외부에 보여지는 방식과 내부에 저장되는 방식을 분리 할 수 있다는 것이다.

예를 들면 클라이언트 단에서 완전히 다른 포맷으로 저장하더라도 JSON 형태의 데이터를 요청할 수 있다. 다양한 URI를 사용하지만 HTTP method는 하나만 사용한다. 그나마 URI를 통해 요청이나 파라미터를 명시한다.

### LEVEL 2 HTTP Method

<img src="https://t1.daumcdn.net/cfile/tistory/99ECE0415BD6CD1612" width="500" heigth="300">

LEVEL 1의 URL + HTTP Method 조합으로 리소스를 구분하는 것으로 응답 상태를 HTTP Status code 를 활용한다. 현재 가장 많은 Restful API가 이 단계를 제공한다.
```
GET /doctors/mjones/slots?date=20100104&status=open HTTP/1.1
Host: royalhope.nhs.uk
```
발생한 에러의 종류를 커뮤니케이션하기 위해 상태 코드(status code)를 사용하는 것, 그리고 안전한 오퍼레이션(GET 등)과 안전하지 않은 오퍼레이션 간의 강한 분리를 제공하는 것이 레벨 2의 핵심 요소이다.

우선, Status Code를 사용한다는 것은 어떤 의미일까?

기존에는 유저 생성 요청을 했을 경우 302 등의 Redirect Request을 서버에서 내려주는 방식이었다면, 지금은 201(created)로 유저 생성 성공을 알릴 뿐 페이지 이동은 Client 단에서 해결하는 방식이다. (login의 경우 성공은 200, 실패 시 인증 실패는 401, 성공했으나 권한 문제시엔 403 등) 즉, 서버는 순수하게 api로서의 기능만을 제공하게 된다. View는 Client에서 알아서 표시한다.

두번째로, 강한 분리를 제공하는 것이 어떤 이점이 있는 것일까?

HTTP에서 GET은 멱등방식(Idempotent )으로 자원을 추출하는데, 이 말은 호출 순서나 횟수에 영향 받지 않고 매번 같은 결과를 받을 수 있게 한다. 그렇기 때문에 모든 request에 '캐싱'기능을 지원하는 다양한 방법을 제공한다.

### LEVEL 3 HYPERMEDIA CONTROL

<img src="https://t1.daumcdn.net/cfile/tistory/99FA253A5BD6CD3028" width="500" heigth="300">
HATEOAS(Hypertext As The Engine Of Application State)

하이퍼미디어란 하나의 컨텐츠가 텍스트나 이미지, 사운드와 같은 다양한 포맷의 컨텐츠를 링크하는 개념이다. 이것은 관련 컨텐츠를 보기 위해 링크를 따라가는 방식과 유사한 방식으로 동작하는데, 클라이언트가 다른 자원에 대한 링크를 통해 서버와 (잠재적으로 상태 변이를 초래하는) 상호작용을 한다.

즉, 특정 API를 요청한 후 다음 단계로 할 수 있는 작업을 알려주는 것이며, 다음 단계의 작업을 위한 리소스의 URI를 알려주는 것이다. 이 단계를 적용하면 클라이언트에 영향을 미치지 않으면서 서버를 변경하는 것이 가능하다.
```javascript
GET /doctors/mjones/slots?date=20100104&status=open HTTP/1.1

Host: royalhope.nhs.uk

HTTP/1.1 200 OK
[various headers]
<openSlotList>
​
 <slot id = "1234" doctor = "mjones" start = "1400" end = "1450">
            <link rel = "/linkrels/slot/book"
            uri = "/slots/1234"/>
 </slot>
 <slot id = "5678" doctor = "mjones" start = "1600" end = "1650">
            <link rel = "/linkrels/slot/book"
            uri = "/slots/5678"/>
 </slot>
</openSlotList>
```
단점은, 클라이언트가 수행할 작업을 찾기 위해 링크를 따라가기 때문에 컨트롤 탐색에 꽤 많은 호출이 발생할 수 있다는 것이다. 또한 복잡성이 증가할 수 있으며, HTTP 요청 상에 나타나는 부하로 낮은 지연시간이 요구될 때 문제가 될 수 있다. HTTP 기반의 REST Payload는 JSON 또는 바이너리 등의 포맷을 지원하므로 실제로 SOAP 보다 훨씬 간결할 수 있지만 Thrift와 같은 바이너리 프로토콜에는 상대가 되지 못한다.

또한 WebSocket의 경우 HTTP 초기 handshake 후에 클라이언트와 서버 간에 TCP 접속이 이루어지고 브라우저에서 스트림 데이터를 보낼 때 효율적일 수 있다. 따라서 HTTP가 대규모 트래픽에는 적합할 수 있지만 TCP 또는 다른 네트워킹 기술 기반의 프로토콜과 비교하면 낮은 지연시간이 필요한 통신에는 그다지 좋은 선택이 아닐 수 있다.

이러한 단점에도 HTTP 기반의 REST는 서비스 대 서비스의 상호작용을 위해 합리적이고 기본적인 선택이다.

### RMM을 통한 REST 핵심 요소
- 자원(RESOURCE) - URI  , 애플리케이션에서 제공하는 resource를 어떻게 분할하고 통합할 것인가?, ( LEVEL 1의 고민 )

- 행위(METHOD) - HTTP METHOD, STATUS CODE , 에러 상황에 대한 적절한 처리와 method 도입을 통한 불필요한 다양성(복잡성) 제거 (LEVEL 2의 고민)

- 표현(Representations) -  hypermedia, API가 스스로 문서화 가능하도록 지원할 것인가? ( LEVEL 3의 고민 )
