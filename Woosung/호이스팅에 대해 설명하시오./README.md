## Hoisting(호이스팅)이란?

- 자바스크립트의 모든 함수에는 자체 `실행 컨텍스트`가 존재한다.
- 이 실행 컨텍스트는 우선 `함수가 실행되는 환경`이라고 생각하면 된다.
- 모든 실행 컨텍스트에는 `생성 단계`와 `실행 단계`가 존재하는데, 이 두 단계 중에서 `생성 단계에서 호이스팅이 발생`한다.
- 호이스팅은 실행 단계에서 동작하는 코드 중 해당 범위의 `모든 선언들이 맨 위로 이동하는 동작을 의미`한다.

```jsx
function myFuncion() {
  console.log(a); // 출력 : undefined
  var a = "hello";
  console.log(a); // 출력 : hello
}
```

### undefined로 출력 된 이유?

- 자바스크립트에서 코드가 동작할 때, 자바스크립트 엔진은 먼저 코드를 살펴보고 `var`와 `function` 선언문들을 찾아 해당 범위의 맨 위로 올리고 undefined로 초기화한다.
- 그러고 나서 실제 명령문을 실행시킨다.
- 즉, 호이스팅으로 인해 해당 범위에 있는 `var` 선언문은 최상단으로 올려지고 undefined으로 초기화가 된 것이라고 보면 된다.

```jsx
function myFuncion() {
  // 호이스팅 발생!
  // var a = undefined;
  console.log(a); // 출력 : undefined
  var a = "hello";
  console.log(a); // 출력 : hello
}
```

### 함수에서의 호이스팅은 어떻게 일어나는가?

- 함수를 표현하는 방식에는 `함수 선언식`, `함수 표현식` 두 가지가 존재한다.

**함수 선언식**

- 함수 선언식에서는 호이스팅이 발생한다.

```jsx
console.log(myFuncion()); // 출력 : 저는 함수 선언식입니다.
function myFuncion() {
  return "저는 함수 선언식입니다.";
}
```

- 실제 코드가 동작했을 때, 호이스팅이 발생해서 코드 최상단에 함수 선언문이 올라가 있는 것을 볼 수 있다.

```jsx
// 호이스팅 발생!
function myFuncion() {
  return "저는 함수 선언식입니다.";
}
console.log(myFuncion()); // 출력 : 저는 함수 선언식입니다.
```

**함수 표현식**

- 반면 함수 표현식에서는 에러가 나는 것을 볼수 있다.

```jsx
console.log(myFuncion()); // TypeError: myFuncion is not a function
var myFuncion = function () {
  return "저는 함수 표현식입니다.";
};
```

- 함수 표현식에서도 호이스팅은 일어난다.
- 그런데 오류가 발생한 이유는 함수를 담은 변수는 최상단으로 올라가지만, 함수는 밑에서 할당되기 때문이다.

```jsx
// 호이스팅 발생!
var myFuncion;
console.log(myFuncion); // 출력 : undefined
console.log(myFuncion()); // TypeError: myFuncion is not a function
myFuncion = function () {
  return "저는 함수 표현식입니다.";
};
```

## const, let 및 var가 호이스팅 할 때 동일한 방식으로 동작하는가?

- var와 마찬가지로 const와 let 또한 호이스팅이 발생한다.
- 하지만 var와 const, let은 동일한 방식으로 동작하지 않는다.

```jsx
console.log(name); // throws a ReferenceError
const name = "Lim";
```

- undefined로 초기화 되는 var와 달리 const와 let은 초기화가 되지 않는 점에서 차이가 있다.
- 출력 결과는 var처럼 undefined를 반환하지 않고, ReferenceError가 발생!
