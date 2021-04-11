## 이벤트 루프란?

- `Call Stack`에 작업이 비어있는지 확인하고, `Callback Queue`에 쌓여있는 작업들을 순서대로 `Call Stack`에 옮겨주는 아이라고 생각하면 된다.
- 하지만 이벤트 루프를 이해하기 위해서는 먼저 자바스크립트가 브라우저에서 어떻게 동작하는지 알아야한다.

### 자바스크립트는 어떻게 동작하는가?

- 자바스크립트는 `싱글 스레드 언어` 혹은 `동기 프로그래밍 모델`이다.
- 즉 이말은 자바스크립트는 `한 번에 하나의 작업만을 수행할 수 있다는 뜻`이다.

```jsx
// 자바스크립트 동작 예제
console.log(1);
console.log(2);
console.log(hello());
console.log(4);

function hello() {
  console.log(3);
}
```

- 예제를 보면 코드에서 첫번째 줄부터 순서대로 `Call Stack`에 쌓여 실행할 것이다.
- 여기서 Call Stack은 `현재 실행중인 명령문에 대한 어떤 순서로 작업을 수행하는지 기록하는 데이터 구조`를 말한다.

![화면-기록-2021-04-09-오후-6 13 42](https://user-images.githubusercontent.com/47416686/114293700-2b4a5200-9ad3-11eb-90bc-df36e4707d57.gif)

- 실행 결과 실행문들이 `Call Stack`에서 차곡차곡 쌓여 실행되는 것을 볼수 있다.

### 자바스크립트가 브라우저에서 어떻게 비동기 처리를 해주는가?

- 우선 브라우저에는 `Web APIs`, `Callback Queue`, `Event Loop` 등으로 구성되어 있다.

```jsx
console.log("first");

setTimeout(function cb() {
  console.log("second");
}, 1000); // 1초 뒤 실행

console.log("third");
```

위 코드가 브라우저에서 동작하는 원리

1. console.log('first'); `Call Stack`에 push되고 'first'가 출력된 후 pop된다.
2. setTimeout은 브라우저에 의해 제공된 API이기 때문에 자바스크립트 엔진에서 처리하지 않는다.
3. setTimeout 함수가 `Call Stack`에 쌓이자마자 그 안에 있던 콜백 함수 cd()가 바로 `web Apis`로 넘겨진다.
4. 그러고 바로 console.log('third'); `Call Stack`에 push되고 'third'가 출력된 후 pop된다.
5. 이제 코드가 모두 실행되었으므로 `Call Stack`은 비어지게 되고, `web Apis`에 있던 cd()는 1초 뒤에 `Callback Queue`로 옮겨진다.
6. 이때 바로 `Event Loop`가 `Call Stack`이 비어지는지와 `Callback Queue`에 쌓여있는 작업들을 확인해서 순차적으로 스택에 작업물을 push한다.
7. 현재 스택이 비어있고, `Callback Queue`에 cd()가 있기 때문에 `Event Loop`가 `Call Stack`에 cd()를 push하고 console.log('second');가 실행하고 'second'가 출력된 후 pop된다.

![브라우저 동작 원리](https://user-images.githubusercontent.com/47416686/114293896-09ea6580-9ad5-11eb-8550-ccc8163dfc8d.gif)

- 즉, 브라우저에서 자바스크립트는 이렇게 작동한다.
- 함수를 동기 호출하면 `Call Stack`에 차곡차곡 쌓여 순차적으로 실행된다.
- 이때 만약에 AJAX나 setTimeout() 혹은 DOM Event 함수를 실행하면, 자바스크립트 엔진은 `Call Stack`에서 `web Apis`로 보내고 정해진 시간 혹은 이벤트가 발생한 순간에 순차적으로 `Callback Queue`에 적재된다.
- `Callback Queue` 에 줄을 선 작업들은 `Call Stack` 에 쌓여있는 작업들이 모두 실행되어 없어지면, 이때 `Event Loop`가 확인해서 `Call Stack`에 순서대로 보내진다.
- 차례대로 `Call Stack`에 쌓여서 실행된다.

## 📌 Event Loop 정의

Event Loop의 역할은 간단합니다.

1. `Call Stack`과 `Callback Queue`를 감시합니다.

2. Call Stack이 `비어있을 경우`, Callback queue에서 함수를 꺼내 Call Stack에 추가 합니다.

## ✔️ 참고

[](http://latentflip.com/loupe/?code=dmFyIGEgPSAxMDsKdmFyIGIgPSAxMDsKdmFyIGMgPSAyMDsKY29uc29sZS5sb2coc3VtKGEsYikpOwpjb25zb2xlLmxvZyhjKTsKCmZ1bmN0aW9uIHN1bShhLGIpIHsKICAgIHJldHVybiBhICsgYjsKfQ%3D%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

[[JS/Event Loop] 자바스크립트, 이벤트 루프(Event Loop)와 동시성(concurrency)에 대하여](https://im-developer.tistory.com/113)

[JavaScript 비동기 핵심 Event Loop 정리](https://medium.com/sjk5766/javascript-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%95%B5%EC%8B%AC-event-loop-%EC%A0%95%EB%A6%AC-422eb29231a8)

[Everything You Need to Know About Event Loop in JavaScript](https://blog.usejournal.com/everything-you-need-to-know-about-event-loop-in-javascript-1f14f94e5ab6)
