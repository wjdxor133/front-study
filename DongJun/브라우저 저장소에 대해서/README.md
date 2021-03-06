# 브라우저 저장소에 대해서 설명해 보세요.

## 브라우저 저장소(WebStorage)

- LocalStorage(영구저장소)
- SessionStorage(임시저장소)
- HTML5를 통해 추가되어 나왔다.

### Local, Session 공통

- 최대 10mb, 권장 5mb 크기를 가진다.
- 키-밸류 스토리지 형태이다.
- 현재 도메인의 로컬 Storage객체에 접근해 데이터를 추가한다.
- 서버 전송이 없다.
- JSON.stringify를 사용하여 객체로도 저장 할 수 있다.

#### 사용 방법

> localStorage와 SessionStorage의 사용법은 같다.

```
localStorage.setItem('myName', 'Dongjun'); //추가
or
localStorage.myNmae = 'Dongjun';
const cat = localStorage.getItem('myName'); //조회
localStorage.removeItem('myName'); //삭제
localStorage.clear(); // 전체삭제
```

### LocalStorage

1.  데이터를 영구적으로 보관가능(JS로 직접 지우지 않는다면)하다.
2.  브라우저가 바뀌어도 스토리지를 공유한다.
3.  Window 전역개체의 LocalStorage 컬렉션을 통해 저장 조회 가능.

사용 예) 자동 로그인

### SessionStorage

1. 브라우저가 컨텍스트 내에서만 유지된다.
2. 다른 브라우저로 도메인 접속시에 다른 스토리지를 가진다.
3. Window 전역객체의 SessionStorage 컬렉션을 통해 저장 조회 가능.

사용 예) 비로그인 장바구니, 글쓰기 내용 복구

## 쿠키(Cookie)란

### 쿠키의 특징

- 브라우저 저장소가 나오기 전에 주로 쓰던 파일이다.
- 4kb의 용량 제한, 하나의 사이트에서 최대 20개의 쿠키 가능, 클라이언트 최대 300개 가능
- 만료일을 지정할 수 있어 **반 영구적인** 데이터 저장 가능
- 서버의 요청이 있을 때마다 쿠키고 같이 전송된다.
- 클라이언트의 메모리의 저장된다.
- 문자열만 저장이 가능하다.

#### 사용 방법

> 생성

```javascript
const setCookie = function (name, value, exp) {
  const date = new Date();
  date.setTime(date.getTime() + exp * 24 * 60 * 60 * 1000);
  document.cookie =
    name + "=" + value + ";expires=" + date.toUTCString() + ";path=/";
};
setCookie("expend", "true", 1);
```

> 조회

```javascript
const getCookie = function (name) {
  const value = document.cookie.match("(^|;) ?" + name + "=([^;]*)(;|$)");
  return value ? value[2] : null;
};
const is_expend = getCookie("expend");
```

> 삭제

```javascript
const deleteCookie = function (name) {
  document.cookie = name + "=; expires=Thu, 01 Jan 1999 00:00:10 GMT;";
};
deleteCookie("name");
```

사용 예) 쇼핑몰 장바구니, 오늘하루 동안 팝업 보이지 않기

### 쿠키의 종류

|        이름        |                                               내용                                                |
| :----------------: | :-----------------------------------------------------------------------------------------------: |
|   Session Cookie   |           만료시간(Expire date)설정하고 메모리에만 저장되며 브라우저 종료시 쿠키를 삭제           |
| Persistent Cookie  |               장기간 유지되는 쿠키, 파일로 저장되어 브라우저 종료와 관게없이 사용.                |
|   Secure Cookie    |                          HTTPS에서만 사용, 쿠키 정보가 암호화 되어 전송                           |
| Third-Party Cookie | 방문한 도메인과 다른 도메인의 쿠키, 보통 광고 배너 등을 관리 할 때 유입 경로를 추척하기 위해 사용 |

### 쿠키의 목적

- **세션 관리**
- **개인 설정유지**
- **트래킹 관리**

### 쿠키의 단점

- 공용 PC에서 쿠키값 유출
  - 자바스크립트를 이용하여 document.cookie 값을 탈취할 수 있음
- XSS(크로스 사이트 스크립트) 공격
  - 네트워크를 통해 전송되는 쿠키값을 암호화하지 않고 전송하는 경우 네트워크 스니핑 공격을 통해 쿠키값을 탈취할 수 있음
- 스니핑(Sniffing) 공격

## 세션이란

- 브라우저가 종료되면 만료시간 관게없이 삭제된다
- 세션 ID파일을 만들어서 클라이언트 쿠키에 저장 데이터는 서버 저장
- 성능 저하의 주요 원인이 될 수 있다.

## IndexedDB 란

- Transaction Database 를 사용하여 Key-Value 로 데이터를 관리하며, B-Tree 데이터 구조를 가진다.
- 용량 제한은 특별히 없으나, HDD 저장소 상태 나 브라우저의 상태에 따라서 달라 질 수 있다.
- javascript 가 이해하는 어떠한 값이라도 모두 저장할 수 있다.

_작은 규모의 데이터는 Storage 를 사용하는것이 좋지만, 큰 데이터는 IndexedDB 를 사용하는 것이 여러모로 유리하다._

## 쿠키 vs 로컬 스토리지

- 로컬 스토리지는 모든 HTTP 요청에 데이터를 주고받을 필요가 없다
  LocalStorage를 이용하면 서버간의 전체 트래픽과 낭비되는 대역폭의 양을 줄일 수 있다.
- 위의 내용으로 인해 인터넷 연결이 잘 유지되지 않는 지역에서 Cookie보다 LocalStorage가 더 우수하다
- 쿠키는 대부분의 브라우저에서 사용이 가능하지만 LocalStorage는 HTML4를 사용하는 브라우저 등에서 작동하지 않을 수 있다.

참조

[https://tristan91.tistory.com](https://tristan91.tistory.com/521) <br>
[https://erwinousy.medium.com](https://erwinousy.medium.com/%EC%BF%A0%ED%82%A4-vs-%EB%A1%9C%EC%BB%AC%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80-%EC%B0%A8%EC%9D%B4%EC%A0%90%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C-28b8db2ca7b2)

## 질문의 대한 답변

브라우저 저장소는
LocalStorage그리고 SessionStorage가 있습니다
SessionStorage는 브라우저 컨텍스트 내에서만 유지되고 브라우저가 컨텍스트가 종료되거나 브라우저가 종료되면 데이터가 사라집니다 하지만
LocalStorage는 쿠키와 비슷하게 사용자의 메모리의 저장되기 때문에 JavaScript로 지우지 않는 이상 삭제되지 않습니다
여기서 LocalStorage와 쿠키의 차이점이 있다면
쿠키는 원래 목적이 서버와의 데이터 교환을 위한 텍스트파일이기 때문에 용량이 4kb로 작고 매번 http 요청이 있을 때 마다 같이 서버의 전송되기 때문에 서버의 트레픽이 낭비될 수 있습니다. 하지만 LocalStorage는
최대 저장 용량이 10mb이고 서버의 전송하는 과정이 없기 때문에 서버에 부담이 없습니다.
또한 LocalStorage는 html5버전에서 지원하기 때문에 대부분의 브라우저에서 지원하는 쿠키와 달리 html4버전 이하를 지원하는 브라우저에서는 지원하지 않는 단점이 있습니다.
