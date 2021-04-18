# Javascript 성능 최적화를 위해서 무엇을 사용해보았나?

> "가장 빠른 코드는 실행하지 않는 코드이다."

## 성능 최적화에 필요한 이론과 측정 도구

성능 최적화를 위해서 성능과 관련된 용어와 개념을 이해하는게 먼저라고 한다.
브라우저 로딩 과정, 사용되는 용어, 성능을 측정하는 데에 사용되는 지표를 알아보고
여기서는 성능 최적화 관련해서 간단하게 작성할 예정이라 로딩 과정만 살펴보자

## 브라우저 로딩 과정

브라우저는 웹 페이지에 필요한 리소스를 내려받고 해석한 다음 여러 계산 과정을 거쳐 콘텐츠를 화면에 보여준다.
이를 브라우저의 로딩 과정이라고 하며 다운로드, 파싱, 스타일, 레이아웃, 페인트, 합성으로 나뉜다. 단계마다 어떤 일이 발생하는지 알아보겠다.

1. 파싱
   브라우저에서 웹 페이지를 로드하면 가장 먼저 HTML 파일을 다운로드한다.
   **파싱**은 다운로드한 HTML을 해석하여 **DOM 트리**를 구성하는 단계이다.
   파싱 중에 <script />, <link />, <img />를 발견하면 각 리소스를 요청하고 다운로드한다.
   HTML 또는 리소스에 CSS가 포함된 경우네는 **CSSOM트리** 구성 작업도 함께 진행한다. DOM트리 및 CSSOM 트리가 구성되는 방법은 아래와 같다.

### DOM 트리 구성

파싱이 일어나면 HTML을 해석해 DOM을 생성한 후, 각 DOM 객체를 트리 데이터 구조로 연결해 부모-자식 관계를 갖도록 만든다.

 <body>, <p>, <div> 등 각 태그가 DOM 트리의 노드로 생성되고 자식 노드를 참조한다.
```html
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
    <title>Critical Path</title>
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
  </body>
</html>
```
<img src="https://user-images.githubusercontent.com/35218826/59728721-3422c180-9276-11e9-979f-f79bb3821ef4.png" width="500" height="300"/>

### CSSOM 트리 구성

위 예제에서 style.css처럼 외부 스타일시트 파일이나 내부 스타일시트가 포함되어 있을 경우,
CSS를 해석해 CSSOM 트리를 구성한다. body, p, span 등 선택자가 노드로 생성되고 각 노드는 스타일을 참조한다.

```css
/* style.css */
body {
  font-size: 16px;
}
p {
  font-weight: bold;
}
span {
  color: red;
}
p span {
  display: none;
}
img {
  float: right;
}
```

### 스타일

스타일 단계에서는 파싱 단계에서 생성된 DOM, CSSOM 트리를 가지고 스타일을 매칭시켜주는 과정을 거쳐 렌더 트리를 구성한다.
아래 이미지는 파싱 단계에서 설명한 DOM 트리와 CSSOM 트리를 조합해 렌더 트리가 구성되는 과정을 보여준다.

<img src="https://user-images.githubusercontent.com/35218826/59728723-34bb5800-9276-11e9-9a1e-a4dad5d240fc.png" width="500" height="300"/>

### 레이아웃

레이아웃 단계에서는 노드의 정확한 위치와 크기를 계산한다. 노드의 정확한 크기와 위치를 파악하기 위해 루트부터 노드를 순회하면서 계산하고,
레이아웃 결과로 각 노드의 정확한 위치와 크기를 픽셀값으로 렌더트리에 반영한다.
아래는 레이아웃 전/후 과정을 보여준다. 만약 CSS에서 크기 값을 %로 지정하였다면, 레이아웃 단계를 거친 후 % 값은 계산되고 측정 가능한 픽셀 단위로 변환된다.

<img src="https://user-images.githubusercontent.com/35218826/59728724-34bb5800-9276-11e9-8f27-219e65664b66.png" width="500" height="300"/>

<img src="https://user-images.githubusercontent.com/35218826/59728725-34bb5800-9276-11e9-9a4e-e26a649523a7.png" width="500" height="300"/>

### 페인트

이전 레이아웃 단계에서 계산된 값을 이용해 렌더트리의 각 노드를 화면상의 실제 픽셀로 변환한다. 이때 위치와 관계없는 CSS 속성(색상, 투명도 등)을 적용한다.
그리고 픽셀로 변환된 결과는 포토샵의 레이어처럼 생성되어 개별 레이어로 관리된다.
단, 각각의 엘리먼트가 모두 레이어가 되는 것은 아니다. transform 속성 등을 사용하면 엘리먼트가 레이어화 되는데, 이 과정을 페인트라고 한다.

### 합성 & 렌더

페인트 단계에서 생성된 레이어를 합성하여 스크린을 업데이트한다. 합성과 렌더 단계가 끝나면 화면에서 웹 페이지를 볼 수 있다.

다음 그림은 브라우저 로딩 과정을 나타낸다. 웹 애플리케이션에서 성능 개선점을 찾기 위해서는 브라우저 로딩 과정을 이해해야 한다.

<img src="https://user-images.githubusercontent.com/35218826/59728726-3553ee80-9276-11e9-9c6e-ac1ff99a01ee.png" width="500" height="300"/>

## 웹 페이지 로딩 최적화

### 블록 리소스(CSS, 자바스크립트) 최적화

브라우저 로딩 과정에서 파싱 중 블록 리소스가 발생할 수 있으며, CSS와 자바스크립트가 블록 리소스에 해당한다고 했다.
최적화의 첫 번째 단계는 이 블록 리소스를 최적화하는 것이다.

## CSS 최적화

렌더 트리를 구성하기 위해서는 DOM 트리와 CSSOM 트리가 필요하다. DOM 트리는 파싱 중에 태그를 발견할 때마다 순차적으로 구성할 수 있지만,
CSSOM 트리는 CSS를 모두 해석해야 구성할 수 있다. 즉, CSSOM 트리가 구성되지 않으면 렌더 트리를 만들지 못하고 렌더링이 차단된다.
이러한 이유로 CSS는 렌더링 차단 리소스라고 하며, 렌더링이 차단되지 않도록 CSS는 항상 HTML 문서 최상단(<head> 아래)에 배치한다.

```html
<head>
  <link href="style.css" rel="stylesheet" />
</head>
```

그리고 특정 조건에서만 필요한 CSS가 있을 때 미디어 쿼리를 사용하면 불필요한 블로킹을 방지할 수 있다. 예를 들어 다음과 같이 페이지를 인쇄하거나(print.css) 화면이 세로 모드일 때(portrait.css) 사용하는 CSS가 있을 때,
해당 스타일을 사용하는 경우에만 로드할 수 있도록 <script> 태그에 media 속성을 명시하여 사용한다.
**미디어 쿼리를 사용하지 않는 경우 (최적화 전)**

```html
<link href="style.css" rel="stylesheet" />
<link href="print.css" rel="stylesheet" />
<link href="portrait.css" rel="stylesheet" />
```

**미디어 쿼리를 사용하지 않는 경우 (최적화 후)**

```html
<link href="style.css" rel="stylesheet" />
<link href="print.css" rel="stylesheet" media="print" />
<link href="portrait.css" rel="stylesheet" media="orientation:portrait" />
```

또한 외부 스타일시트를 가져올 때 사용하는 @import 사용은 피한다.
아래와 같이 @import를 사용했을 때 브라우저는 스타일시트를 병렬로 다운로드 할 수 없기 때문에 로드 시간이 늘어날 수 있다.

```html
/* foo.css */ @import url("bar.css")
```

## 자바스크립트 최적화

자바스크립트는 DOM 트리와 CSSOM 트리를 동적으로 변경할 수 있기 때문에 HTML 파싱을 차단하는 블록 리소스이다.

<script> 태그를 만나면 스크립트가 실행되며 그 이전까지 생성된 DOM에만 접근할 수 있다. 그리고 스크립트 실행이 완료될 때까지 DOM 트리 생성이 중단된다. 
외부에서 가져오는 자바스크립트의 경우에는 모든 스크립트가 다운로드되고 실행될 때까지 DOM 트리 생성이 중단된다. 
이러한 이유로 자바스크립트도 렌더링 차단 리소스라고 하며, HTML 문서 최하단(<body> 직전)에 배치한다.

```html
<body>
  <div>...</div>
  <div>...</div>
  <script src="app.js" type="text/javascript"></script>
</body>
```
만약 <head> 아래에 포함되어 있거나 HTML 내부에 <script> 태그가 포함되어 있을 때도 HTML 파싱을 멈추지 않게 할 수 있다. 
<script> 태그에 defer나 async 속성을 명시하면 스크립트가 DOM 트리와 CSSOM 트리를 변경하지 않겠다는 의미이기 때문에 브라우저가 파싱을 멈추지 않는다. 
단, 이 속성들은 브라우저 지원 범위가 한정적이므로 사용에 유의한다.

```html
<html>
  <head>
    <script
      async
      src="https://google.com/analatics.js"
      type="text/javascript"
    ></script>
  </head>
  <body>
    <div>...</div>
    <div>...</div>
  </body>
</html>
```

### 리소스 요청 수 줄이기

CSS, 자바스크립트, 이미지 등 웹 페이지에 포함된 리소스는 서버 요청 후 다운로드되어야 사용할 수 있다.
다음 이미지는 개발자 도구 네트워크 패널에서 1개 리소스 파일을 요청했을 때 걸리는 시간을 확인한 것이다.
이 파일의 실제 다운로드 시간은 1.03ms, 그 외 대기 시간(전체 소요 시간 - 실제 다운로드 시간)은 127.45ms가 소요된다.
이렇게 리소스 파일 하나를 요청하는 데 많은 시간이 소요되므로, 필요한 요청만 할 수 있도록 최적화해야 한다.
리소스 종류별로 다른 요청 수를 줄이는 방법을 설명한다.

<img src="https://user-images.githubusercontent.com/35218826/59728744-384edf00-9276-11e9-802d-6b7c5f9866fe.png" width="500" height="300"/>

### 이미지 스프라이트

다음과 같은 웹 페이지에서 아이콘마다 다른 이미지 파일을 사용할 경우 리소스 요청이 7번 이상 발생한다.
이런 경우 이미지 스프라이트 기법을 사용하여 요청을 1번으로 줄일 수 있다.

<img src="https://user-images.githubusercontent.com/35218826/59728745-38e77580-9276-11e9-97b8-8ceaf50e3379.png" width="360" height="250"/>

이미지 스프라이트는 여러 개 이미지를 하나로 만들고, CSS의 background-position 속성을 사용해 부분 이미지를 사용하는 방법이다.
아래 CSS에서 사용된 icons-sprite.png가 스프라이트 이미지다.
이 이미지 스프라이트 기법을 사용하면 웹 페이지를 보다 빨리 보여줄 수 있다.

```css
<button class="btn" > 확인</button > .btn {
  background-image: url(../images/icon-sprite.png);
  background-position: 10px 10px;
  width: 20px;
  height: 20px;
}
```

### CSS, 자바스크립트 번들하기

모듈 기반의 개발 방식이 등장하기 이전까지 분리된 여러 개의 리소스 파일을 가져와 사용했었다. 아래 최적화 하기 전 예제를 살펴보면,
5번 이상의 리소스 요청(CSS 파일 2번, 자바스크립트 파일 3번)이 발생한다. 이 경우에는 webpack과 같은 번들러를 사용하여 CSS, 자바스크립트 파일 요청을 줄일 수 있다.
번들러는 여러 개의 모듈 파일을 하나로 묶어서 1개 파일로 생성해주는데 이것을 번들 파일이라고 한다.
이 번들 파일을 사용하여 리소스 요청을 줄일 수 있다.

**분리된 리소스를 사용하는 경우 (최적화 전)**

```html
<html>
  <head>
    <link href="foo.css" rel="stylesheet" />
    <link href="bar_baz.css" rel="stylesheet" />
  </head>
  <body>
    <div id="foo">...</div>
    <script async src="foo.js" type="text/javascript"></script>
    <script async src="bar.js" type="text/javascript"></script>
    <script async src="baz.js" type="text/javascript"></script>
  </body>
</html>
```

**분리된 리소스를 사용하는 경우 (최적화 후)**

```html
<html>
  <head>
    <link href="bundle.css" rel="stylesheet" />
  </head>
  <body>
    <div class="foo">...</div>
    <script async src="bundle.js" type="text/javascript"></script>
  </body>
</html>
```

### 내부 스타일시트 사용하기

<link> 태그로 외부 스타일시트를 가져오는 대신, 문서 안에서 <style> 태그를 사용할 수 있다. 이러한 사용 방법을 내부 스타일시트라고 하며, 
외부 스타일시트를 가져올 때 발생하는 요청 횟수를 줄일 수 있다. 단, 내부 스타일시트를 사용하면 리소스 캐시를 사용할 수 없어서 HTML에 CSS가 매번 포함되므로 필요한 경우에만 사용한다.

**외부 스타일시트를 사용하는 경우 (최적화 전)**

```html
<html>
  <head>
    <link href="bundle.css" rel="stylesheet" />
  </head>
  <body>
    <div class="foo">...</div>
  </body>
</html>
```

**내부 스타일시트를 사용하는 경우 (최적화 후)**

```html
<html>
  <head>
    <style type="text/css">
      .foo {
        background-color: red;
      }
    </style>
  </head>
  <body>
    <div class="foo">...</div>
  </body>
</html>
```

### 작은 이미지를 HTML, CSS로 대체

웹 페이지에서 사용하는 아이콘 이미지 개수가 적은 경우, 다운로드한 이미지를 사용하는 대신 이미지를 HTML, CSS에 포함해 사용할 수 있다.
Data URI로 처리할 수 있으며, 다음과 같이 HTML, CSS에서 외부 경로로 이미지를 가져오던 부분을 Base64로 변환된 URI로 대체한다.
이렇게 하면 외부 이미지를 사용하기 위해 발생하는 요청 횟수를 줄일 수 있다.
이 경우도 내부 스타일시트를 사용했을 때와 같이 캐시 문제가 있으므로 필요한 경우에만 사용한다.

**외부 이미지 사용 (최적화 전)**

```css
.btn{background: url('../img/arrow_top.png') no-repeat 0 0;}
<img src="../img/arrow_top.png" />
```

**이미지를 Base64로 변환하여 사용 (최적화 후)**

```css
.btn{background: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAwAAAAOCAYAAAAbvf3sAAAAAXNSR0IArs4c6QAAAHBJREFUKBVjYBimICwsLAaEsXmPGV0QqnAeUNxfW1v7/tWrVy8hq0HRgKQ4CahoIxDPQ9cE14CseNWqVUtAJoMUo2tiBFkXGRmp9/fv3zNAZhJIMUgMBmAGMTMzmyxfvhzhPJAmmCJ0Gp8cutqhwAcASWgwk+79LiQAAAAASUVORK5CYII=') no-repeat 0 0;}

<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAwAAAAOCAYAAAAbvf3sAAAAAXNSR0IArs4c6QAAAHBJREFUKBVjYBimICwsLAaEsXmPGV0QqnAeUNxfW1v7/tWrVy8hq0HRgKQ4CahoIxDPQ9cE14CseNWqVUtAJoMUo2tiBFkXGRmp9/fv3zNAZhJIMUgMBmAGMTMzmyxfvhzhPJAmmCJ0Gp8cutqhwAcASWgwk+79LiQAAAAASUVORK5CYII=" />
```

## 리소스 용량 줄이기

용량이 큰 리소스도 웹 페이지 로딩 시간을 느리게 하는 원인이 된다.
각 리소스에 맞게 불필요한 데이터를 제거하고 압축하여 사용하는 것이 좋다. 용량을 줄이기 위한 최적화 방법을 알아보겠다.

### 중복 코드 제거하기

자바스크립트 코드 중 자주 사용되는 코드는 utils.js 파일로 정리해 사용한다. 중복 코드로 인해 용량이 늘어나는 문제를 막을 수 있다.

**중복 코드 사용 (최적화 전)**

```javascript
// foo.js
function filter() { ... }
function map() { ... }

filter();
map();
// bar.js
function filter() { ... }
function find() { ... }

filter();
find();
```

**중복 코드 제거 (최적화 후)**

```javascript
// utils.js
export function find() { ... }
export function filter() { ... }
export function map() { ... }
// foo.js
import {filter, map} from 'utils.js'

filter();
map();
// bar.js
import {filter, find} from 'utils.js'

filter();
find();
```

### 만능 유틸 사용 주의하기

loadsh와 같은 만능 유틸 라이브러리를 사용할 때 주의해야한다. 일반적인 방식으로 가져와 사용하면 유틸 함수 전체가 포함되므로 자바스크립트 파일 용량이 커진다.
이 경우에 필요한 함수만 부분적으로 가져올 수 있으며 용량이 늘어나는 문제를 해결해준다.
그리고 되도록 사용하지 않는 기능이 많이 포함된 라이브러리 사용은 지양한다.

**모든 유틸 함수 가져오기 (최적화 전)**

```javascript
import _ from 'lodash';

_.array(...);
_.object(...);
```

**필요한 함수만 가져오기 (최적화 후)**

```javascript
import array from 'lodash/array';
import object from 'lodash/fp/object';

array(...);
object(...);
```

### HTML 마크업 최적화

HTML은 태그의 중첩을 최소화하여 단순하게 구성한다. 또한 공백, 주석 등을 제거하여 사용한다.
권장하는 DOM 트리의 노드 수는 전체 1500개 미만, 최대 깊이는 32개, 자식 노드를 가지는 부모 노드는 60개 미만이다.
불필요한 마크업 사용으로 인해 DOM 트리가 커지는 것을 막고, HTML 파일 용량이 늘어나지 않도록 해야 한다.

### 간결한 CSS 선택자 사용

스타일을 적용할 때 간결한 CSS 선택자를 사용해 최적화한다.
ID 대신 클래스 선택자를 사용하면 중복되는 스타일을 묶어서 처리할 수 있다. 선택자는 최소화여 사용한다.

**불필요한 셀렉터 사용 (최적화 전)**

```html
<html>
  <head>
    <style type="text/css">
      #wrapper {
        border: 1px solid blue;
      }

      #wrapper #foo {
        color: red;
        font-size: 15px;
      }

      #wrapper #bar {
        color: red;
        font-size: 15px;
        font-weight: bold;
      }

      #wrapper #bar > span {
        color: blue;
        font-weight: normal;
      }
    </style>
  </head>
  <body>
    <div id="wrapper">
      <span id="foo">hello</span>
      <span id="bar"> javascript <span>world</span> </span>
    </div>
  </body>
</html>
```

**간결한 셀렉터 사용 (최적화 후)**

```html
<html>
  <head>
    <style type="text/css">
      .wrapper {
        border: 1px solid blue;
      }

      .text {
        color: red;
        font-size: 15px;
      }

      .strong {
        font-weight: bold;
      }

      .wrapper .text {
        color: blue;
        font-weight: normal;
      }
    </style>
  </head>
  <body>
    <div class="wrapper">
      <span class="text">hello</span>
      <span class="text strong">
        javascript <span class="text">world</span>
      </span>
    </div>
  </body>
</html>
```

## 웹 페이지 렌더링 최적화

웹 페이지를 렌더링하기 위해서는 DOM과 CSS가 필요하다. 그러나 다양한 기능과 효과를 구현하기 위해서 자바스크립트를 많이 사용하기 때문에,
자바스크립트가 렌더링 성능에 어떤 영향을 주는지 잘 알아야 한다. 또한 자바스크립트는 브라우저에서 단일 스레드로 동작하기 때문에 자바스크립트의
실행 시간은 곧 렌더링 성능과 직결된다. 렌더링은 자바스크립트의 실행 시간과 자바스크립트로 인한 DOM, CSS 변경을 다시 화면에 그리는 시간을 모두 포함한다.
렌더링 성능 최적화는이러한 소요 시간을 단축하고 화면에 끊김 없이 그리는 것이다. 이번에는 브라우저 렌더링 과정에서 어떤 부분이
성능에 영향을 주고, 특히 자바스크립트에서 실행되는 일련의 코드가 렌더링 성능에 어떠한 영향과 최적화할 방법을 알아본다

### 레이아웃 최적화

렌더링 과정에서 레이아웃은 DOM 요소들이 화면에 어느 위치에 어떤 크기로 배치될지를 결정하게 되는 계산 과정이다.
자바스크립트 코드를 통해 DOM을 변경하거나 스타일을 변경할 경우, 아래 그림같이 변경된 스타일을 반영하고 다시 레이아웃을 해야만 화면에 렌더링 할 수 있다.
특히 레이아웃은 글자의 크기를 일일이 계산하고 요소 간 관계를 모두 파악해야 하는 과정이므로 시간이 오래 걸린다.

<img src="https://user-images.githubusercontent.com/35218826/59728727-3553ee80-9276-11e9-9a19-7e6af410a139.jpg" width="360" height="250"/>

레이아웃 최적화의 목표는 자바스크립트 실행 과정과 렌더링이 다시 일어나는 과정에서 레이아웃에 걸리는 시간을 단축하고 레이아웃이 최대한 발생하지 않도록 하는 것이다.
레이아웃을 최대한 적게하고 리페인트만 할 수 있도록, 자바스크립트와 HTML, CSS 측면에서 최적화 방법을 하나씩 알아본다.

### 자바스크립트 실행 최적화

자바스크립트 실행 시간이 긴 경우, 한 프레임 처리가 오래 걸려 렌더링 성능이 떨어진다. 많은 작업을 수행할 때 자바스크립트 실행 시간은 당연히 오래 걸린다.
그러나 코드가 단순하더라도 불필요한 레이아웃으로 인해 실행 시간이 오래 걸릴 수 있으므로 성능 저하의 원인을 잘 파악해야 한다.
또한 레이아웃을 줄일 수 있도록 DOM 및 스타일 변경을 최소화해야 한다.

#### 강제 동기 레이아웃 최적화

DOM의 속성을 변경하면 화면 업데이트를 위해 레이아웃이 일어날 수 있다.
원래 레이아웃은 비동기이나 특정 상황에서 동기적으로 레이아웃이 발생할 수 있다. 특정 속성을 읽을 때 최신 값을 계산하기 위해 레이아웃이 동기적으로 발생하며 이를 강제 동기 레이아웃이라고 한다.
강제 동기 레이아웃은 자바스크립트 실행 시간을 늘어나게 하므로 신경 써야 한다. 강제 동기 레이아웃이 일어나는 경우와 개선 방법은 다음과 같다

#### 강제 동기 레이아웃 피하기

스타일을 변경한 다음 offsetHeight, offsetTop과 같은 계산된 값을 속성으로 읽을 때 강제로 동기 레이아웃을 수행해야 한다.

```javascript
const tabBtn = document.getElementById("tab_btn");

tabBtn.style.fontSize = "24px";
console.log(testBlock.offsetTop); // offsetTop 호출 직전 브라우저 내부에서는 동기 레이아웃이 발생한다.
tabBtn.style.margin = "10px";
// 레이아웃
```

계산된 값을 반환하기 전에 변경된 스타일이 계산 결과에 적용되어 있지 않으면 변경 이전 값을 반환하기 때문에 브라우저는 동기로 레이아웃을 해야만 한다.
최신 브라우저에도 동일하게 발생하는 부분이므로 강제 동기 레이아웃을 발생할 수 있는 코드를 최대한 사용하지 않도록 주의해야 한다.

#### 레이아웃 스래싱(thrashing) 피하기

한 프레임 내에서 강제 동기 레이아웃이 연속적으로 발생하면 성능이 더욱 저하된다.
다음 코드에서는 paragraphs[i] 요소를 순회하면서 각 요소의 너비를 box 요소의 너비와 일치하도록 설정한다.
반복문 안에서 style.width를 설정하고 box.offsetWidth를 읽어오면 for문이 반복 실행될 때마다 레이아웃이 발생한다.
이것을 레이아웃 스래싱이라고 한다. 반복문 밖에서 box 엘리먼트의 너비를 읽어오면 레이아웃 스래싱을 막을 수 있다.

```javascript
function resizeAllParagraphs() {
  const box = document.getElementById("box");
  const paragraphs = document.querySelectorAll(".paragraph");

  for (let i = 0; i < paragraphs.length; i += 1) {
    paragraphs[i].style.width = box.offsetWidth + "px";
  }
}
// 레이아웃 스래싱을 개선한 코드
function resizeAllParagraphs() {
  const box = document.getElementById("box");
  const paragraphs = document.querySelectorAll(".paragraph");
  const width = box.offsetWidth;

  for (let i = 0; i < paragraphs.length; i += 1) {
    paragraphs[i].style.width = width + "px";
  }
}
```

### 렌더링 최적화를 위한 방법

- 가능한 한 하위 노드의 DOM을 조작하고 스타일을 변경

  - DOM 트리 상위 노드의 스타일을 변경하면 하위 노드에 모두 영향을 미친다.
  - 변경 범위를 최소화할수록 레이아웃 범위가 줄어든다.

- 영향받는 엘리먼트 제한

  - 부모-자식 관계 : 부모 엘리먼트의 높이가 가변적인 상태에서 자식 엘리먼트의 높이를 변경할 경우, 부모 엘리먼트부터 레이아웃이 다시 일어난다.
    이때 부모 엘리먼트의 높이를 고정하여 사용하면 하단에 있는 엘리먼트는 영향을 받지 않게 된다.
    예를 들어 높이가 모두 다른 여러 개의 탭 콘텐츠가 있을 때, 부모 엘리먼트(탭 컨테이너)의 높이를 고정하여 사용한다.
  - 같은 위치에 있는 엘리먼트 : 여러 개의 엘리먼트가 인라인(inline)으로 놓여 있을 때
    첫 번째 엘리먼트의 width 값 변경으로 인해 나머지 엘리먼트의 위치 변경이 일어나므로 유의한다.

- 숨겨진 엘리먼트 수정

  - display: none으로 숨겨진 엘리먼트를 변경할 경우에는 레이아웃, 리페인트가 발생하지 않아 성능에 유리하다.
  - 많은 수의 엘리먼트를 변경해야 할 경우 숨겨진 상태에서 엘리먼트를 변경하고 다시 보이도록 하여 레이아웃 발생을 최대한 줄인다

- CSS 애니메이션 사용
  - position: absolute 처리
    - position을 absolute나 fixed로 설정하면 주변 레이아웃에 영향을 주지 않는다.
  - transform 사용
    - transform을 사용한 엘리먼트는 레이어로 분리되기 때문에 영향받는 엘리먼트가 제한되어 레이아웃과 페인트를 줄일 수 있다
    - left, top을 사용하면 모든 프레임마다 엘리먼트와 배경이 합성되어 많은 시간이 걸리므로, transform: translate()를 사용해야 한다.

### 참조

https://ui.toast.com/fe-guide/ko_PERFORMANCE#%EB%A0%88%EC%9D%B4%EC%95%84%EC%9B%83%EA%B3%BC-%EB%A6%AC%ED%8E%98%EC%9D%B8%ED%8A%B8
