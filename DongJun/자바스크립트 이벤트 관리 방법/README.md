# 자바스크립트 이벤트 관리방법? 보통 어떤 식으로 이벤트를 설계하는지?

ex) 이벤트 캡처링 & 버블링
ex) 이벤트 등록 & 해제
ex) 이벤트 위임 방식 등

## 이벤트란

이벤트 : 브라우저에서 일어나는 일련의 사용자 행동
이벤트 리스너 : 이벤트가 발생했을 때 어떻게 처리할 지 등록된 함수를 통해 정의

## 이벤트 캡처링 & 버블링 단계

> addEventListener 3번째 매개변수 값으로 설정한다.
> ![이벤트 캡처링 & 버블링](https://miro.medium.com/max/664/1*DGinZ3yza-ZZBrhffBLjRQ.png)

### 캡처링(Event Capturing) 단계

- 이벤트를 등록한 DOM Element에 이벤트가 발생한 경우, 이벤트가 발생한 대상 ("Target")을 찾아가기 위해 전역객체인 Window부터 대상객체까지 DOM Tree를 따라 가면서 이벤트를 전달합니다.

### 대상(Event Target) 단계

- 이벤트가 발생한 바로 그 대상에 전달되는 단계

```
(e) => handleChange(e.target.value)
```

> e는 자바스크립트 이벤트 객체를 의미합니다.

### 버블링(Event Bubbling) 단계

- 이벤트가 캡처링되어 대상 DOM 객체 (Target)까지 정상적으로 도달하게 되었을 때, 별도의 처리가 없다면, 이벤트는 다시 부모 노드를 타고 Window객체까지 전달되게 됩니다.

## 이벤트 등록 & 해체

### 이벤트 등록 방법 3가지

- 인라인(Inline) 방식

```
<input type="button" onClick="alert("hello doongji)" />
```

> 태그에 이벤트가 포함되기 때문에 이벤트 소재를 파악하기 쉽지만 html과 js가 혼재된 형태이기 때문에 바람직한 형태는 아니다

- 프로퍼티 리스너

```
<script>

    var t = document.getElementById(‘target’);

    t.onclick = function(){

        alert(‘Hello world’);

    }

</script>
```

> html과 js가 분리된 형태는 선호되는 방식이지만 아래 addEventListener방식을 추천한다

- addEventListener

```
<script>

    var t = document.getElementById(‘target’);

    t.addEventListener(‘click’, function(event){

        alert(‘Hello world, ‘+event.target.value);

    });

</script>
```

> 하나의 이벤트 대상에 복수의 동일 이벤트 타입 리스너를 등록할 수 있다는 점이다.

## 이벤트 위임 방식

- 컨테이너 하나의 핸드러를 할당한다
- 핸들러 event target을 이용해서 발생 위치 확인
- 원하는 요소가 확인되면 핸들러 실행

### 장점

- 많은 핸들러가 필요없기 때문에 단순해지고 메모리가 절약된다.
- 요소를 추가, 삭제 할 때 핸들러를 신경 쓸 필요가 없어 코드가 간단해진다
- innerHtml등 요소 덩어리를 더하거나 뺄 수 있기 때문에 Dom 수정이 쉽다

### 단점

- 낮은 레벨에 할당한 핸드러는 event.stopPropagation()을 쓸 수 없다.
- 무시해도 되는 이벤트도 응답해야 하므로 cpu 부하가 있을 수 있다. (신경 쓸 정도는 아니다)

### 이벤트 델리게이션 패턴

부모 엘리먼트에 하위 엘리먼트의 리스너를 작성하는 방식

---

```
**DOM Level 1**
core, HTML, 그리고 XML 문서모델에 대한 내용이다. 레벨1은 문서에 대하여 항해(navigation)하거나 조작(manipulation)하는 기능을 포함한다.

**DOM Level 2**
스타일 쉬트를 적용한 개체모델을 지원하고 문서에 스타일 정보를 조작하는 기능을 정의 한다. 또한 문서에 대한 풍부한 질의 기능과 이벤트 모델에 대한 정의 기능도 포함한다.

**DOM Level 3**
윈도우즈 환경 하에서 사용가능한 사용자 인터페이스를 기술하는 것까지 포함한다. 이를 이용하여 사용자는 문서의 DTD를 조작하는 기능과 보안 레벨까지 정의할 수 있다.
```

참조

[https://ko.javascript.info/bubbling-and-capturing](https://ko.javascript.info/bubbling-and-capturing) <br>

## 질문의 대한 답변

자바스크립트에는 여러 가지 이벤트 등록방법이 있는데 그 중에서 addEventLisner을 통해서
이벤트 위임을 사용할 수 있습니다 이를 통해 델리게이션 패턴을 구현할 수 있는데
델리게이션 패턴이란 부모 엘리먼트에 하위 엘리먼트의 이벤트 리스너를 작성하는 방식으로
이를 통해서 엏는 장점은 많은 핸들러가 필요없기 때문에 단순해지고 메모리가 절약되고,
단점은 무시해도 되는 이벤트로 응답해야하므로 cpu에 부하가 있을 수 있습니다.
하지만 신경 쓸 정도로 많은 부하가 있는 것은 아닙니다.
저는 앞서 말한 델리게이션 패턴을 이용해서 이벤트를 관리하고
더 좋은 방식으로도 설계하려고 노력하고 있습니다.
