# Event Loop에 대해서 알고있는가

## ECMAScript에는 이벤트 루프가 없다

'ECMAScript 에는 동시성이나 비동기와 관련된 언급이 없다'고 할 수 있겠다 실제로 V8과 같은 자바스크립트 엔진은 단일 호출 스택(Call Stack)을 사용하며,
요청이 들어올 때마다 해당 요청을 순차적으로 호출 스택에 담아 처리할 뿐이다. 그렇다면 비동기 요청은 어떻게 이루어지며,
동시성에 대한 처리는 누가 하는 걸까? 바로 이 자바스크립트 엔진을 구동하는 환경,
즉 브라우저나 Node.js가 담당한다. 먼저 브라우저 환경을 간단하게 그림으로 표현하면 다음과 같다.

## 단일 호출 스택과 Run-to-Completion

이벤트 루프에 대해 알아보기 전에 먼저 자바스크립트 언어의 특징을 하나 살펴본다.
자바스크립트 함수가 실행되는 방식을 보통 'Run to Completion'이라고 한다. 이는 `하나의 함수가 실행되면 이 함수의 실행이 끝날 때까지는 다른 어떤 작업도 중간에 끼어들지 못한다`는 의미이다.

<img src="https://miro.medium.com/max/2048/1*4lHHyfEhVB0LnQ3HlhSs8g.png" width="500" height="300"/>

## JS Engine

자바스크립트 엔진은 `Memory Heap` 과 `Call Stack` 으로 구성되어 있다.
가장 유명한 것이 구글의 V8 Engine이다.
자바스크립트는 `단일 스레드 (sigle thread) 프로그래밍` 언어인데,
이 의미는 `Call Stack이 하나` 라는 이야기이다.

- Memory Heap : 메모리 할당이 일어나는 곳
  (ex, 우리가 프로그램에 선언한 변수, 함수 등이 담겨져 있음)
- Call Stack : 코드가 실행될 때 쌓이는 곳. stack 형태로 쌓임.
  Stack(스택) : 자료구조 중 하나, 선입후출(LIFO, Last In First Out)의 룰을 따른다.

## Web API

그림의 오른쪽에 있는 Wep API는 JS Engine의 밖에 그려져 있다.
즉, 자바스크립트 엔진이 아니다.
`Web API` 는 `브라우저에서 제공하는 API` 로, DOM, Ajax, Timeout 등이 있다.
Call Stack에서 실행된 비동기 함수는 Web API를 호출하고,
Web API는 콜백함수를 Callback Queue에 밀어 넣는다.

## Callback Queue

`비동기적으로 실행된 콜백함수`가 보관 되는 영역이다.
예를 들어 setTimeout에서 타이머 완료 후 실행되는 함수(1st 인자),
addEventListener에서 click 이벤트가 발생했을 때 실행되는 함수(2nd 인자) 등이 보관된다.

- Queue(큐) : 자료 구조 중 하나, 선입선출(FIFO, Frist In Frist OUT)의 룰을 따른다.

## Event Loop

Event Loop는 Call Stack과 Callback Queue의 상태를 체크하여,
`Call Stack이 빈 상태가 되면, Callback Queue의 첫번째 콜백을 Call Stack으로 밀어넣는다.`
이러한 반복적인 행동을 `틱(tick)` 이라 부른다.

아래 코드는 MDN에서 제공하는 Event Loop의 가상코드이다.

```javascript
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

Task Queue는 Stack이 비어있기 전까지 `Event Loop를 돌며 Stack이 비었나 확인한다.`
비어있지 않으면 Queue 안의 API들을 내보내지 않는다.

순서는

- V8 엔진에서 코드가 실행되면, Call Stack에 쌓인다.
- Stack의 선입후출의 룰에 따라 제일 마지막에 들어온 함수가 먼저 실행되며,
  Stack에 쌓여진 함수가 모두 실행된다.
  - 비동기함수가 실행된다면, Web API가 호출된다.
  - Web API는 비동기함수의 콜백함수를 Callback Queue에 밀어넣는다.
  - Event Loop는 Call Stack이 빈 상태가 되면
    Callback Queue에 있는 첫번째 콜백을 Call Stack으로 이동시킨다.
    (이러한 반복적인 행동을 틱(tick)이라 한다.)

## Microtask Queue

<img src="http://sculove.github.io/blog/2018/01/18/javascriptflow/browser-structure.png" width="500" height="300"/>

코드로 간단히 설명해보면

- 아래 코드의 결과는

```javascript
console.log("script start");

setTimeout(function () {
  console.log("setTimeout");
}, 0);

Promise.resolve()
  .then(function () {
    console.log("promise1");
  })
  .then(function () {
    console.log("promise2");
  });

console.log("script end");
```

<details>
    <summary>결과</summary>
    <div markdown="1">
        ```
        script start
        script end
        promise1
        promise2
        setTimeout
        ```
    </div>
</details>
<br/><br/>

Event Loop는 우선적으로 Microtask Queue를 확인한다.
만약 `Microtask Queue에 콜백이 있다면, 이를 먼저 Call Stack에 담는다.`
그리고 Microtask Queue에 더이상 처리해야할 콜백이 없다면,
Task Queue에 확인 후 처리한다.

`Promise의 then()의 콜백` 은 Task Queue가 아닌 `Microtask Queue에 담긴다.`
따라서 위 코드에서는 우선순위가 높은 Microtask Queue부터 처리되므로,
Promise의 then() 콜백이 다 실행되고, setTimeout 콜백이 실행되는 거다.

## Animation Frames

requestAnimationFrame API가 실행되면 콜백이 Animation Frames으로 담긴다.
그럼 Microtask Queue, Task Queue, Animation Frames의 우선순위는 어떨까

- 코드로 확인해보자.

```javascript
console.log("script start");

setTimeout(function () {
  console.log("setTimeout");
}, 0);

Promise.resolve()
  .then(function () {
    console.log("promise1");
  })
  .then(function () {
    console.log("promise2");
  });

requestAnimationFrame(function () {
  console.log("requestAnimationFrame");
});
console.log("script end");
```

<details>
    <summary>결과</summary>
    <div markdown="1">
        ```
        script start
        script end
        promise1
        promise2
        requestAnimationFrame
        setTimeout
        ```
    </div>
    <b>Microtask Queue > Animation Frames > Task Queue 순으로 실행된다.</b>
</details>
<br/><br/>

정리해서

- 코드가 실행되면 Call Stack에 쌓이고, Stack에서는 선입후출 룰 대로 실행된다.
  - 비동기 함수가 실행된다면, Web API가 호출된다.
  - Web API는 비동기 함수의 콜백함수를 Callback Queue에 밀어넣는다.
    - Promise는 Microtask Queue로, Timeout은 Task Queue로,
      RequestAnimationFrame은Animation Frame으로 콜백함수를 밀어넣는다.
    - Event Loop는 Call Stack이 빈 상태가 되면 콜백을 Call Stack으로 이동시킨다
      - 콜백 이동 우선순위는 Microtask Queue > Animation Frames > Task Queue이다.

### 참조

https://meetup.toast.com/posts/89
https://velog.io/@thms200/Event-Loop-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84
