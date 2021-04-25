## Arrow Function

- 자바스크립트를 사용하고 있다면 `화살표 표기법(=>)`를 사용해서 함수를 간결하게 정의하는 함수를 접했을 것이다.
- 사용할 때 매우 편리하지만 상황에 따라 사용해야 할지와 사용하지 말아야 할지를 아는 것은 굉장히 중요하다.
- 즉 `모든 경우 화살표 함수를 사용할 수 있는 것은 아니라는 뜻`이다.

### 사용 방법

1. **매개변수 지정 방법**

```jsx
// 매개변수가 없을 경우
() => { ... }

// 매개변수가 한 개인 경우, 소괄호를 생략할 수 있다.
 x => { ... }

// 매개변수가 여러 개인 경우, 소괄호를 생략할 수 있다.
(x, y) => { ... }
```

2.  **함수 몸체 지정 방법**

```jsx
// single line block
x => { return x * x }

// 함수 몸체가 한줄 일 경우, 중괄호를 생략할 수 있으며 암묵적으로 값이 return 된다.
x => x * x

// 함수 몸체가 여러 줄 일 경우, 중괄호와 return을 명시해줘야 한다.
() => {
	const x = 10;
	return x * x;
}

// 객체 반환할 경우
() => { return { a: 1 }; }
() => ({ a: 1 })  // 위 표현과 동일하다. 객체 반환시 소괄호를 사용한다.
```

### 화살표 함수의 장점

- 함수 표현식보다 구문이 짧고 간결하다.

```jsx
// ES5 function
function add(a, b) {
  return a + b;
}

// ES6 Arrow function
const add = (a, b) => a + b;

// 단 중괄호를 사용할 경우
const add = (a, b) => {
  return a + b;
};
```

- "return"을 명시적으로 작성하지 않아도 암묵적인 반환이 가능하다.

## 일반 함수랑 다른점?

### this

**정적 바인딩**

- 일반 함수 표현식에서 this 키워드는 호출되는 컨텍스트에 따라 다른 값이 바인딩됩니다. → `**동적 바인딩**`
- 반면, 일반 함수와는 다르게 화살표 함수에서의 this는 바인딩이 없으며 화살표 함수가 정의되어있는 객체의 스코프를 그대로 가지고 있다. → `**정적 바인딩**`
- 이 말은 즉, 화살표 함수 내의 this는 `**상위 스코프의 this**`를 의미한다.

```jsx
name = "Arrow function";
let me = {
  name: "Regular function",
  thisInArrow: () => {
    console.log("Example of " + this.name); // 여기 'this'는 바인딩이 일어나지 않는다.
  },
  thisInRegular() {
    console.log("Example of " + this.name); // 여기 'this'는 바인딩이 일어난다.
  },
};
me.thisInArrow(); // Arrow function
me.thisInRegular(); // Regular function
```

- 일반 함수와 달리, 화살표 함수에서 this는 바인딩일 일어나지 않는 것을 볼수있다.

**화살표 함수의 this는 함수가 호출될 때가 아닌 함수가 정의될 때 이미 결정된다.**

```jsx
var arrow_outer = () => {
  console.log(this);
};

var obj = {
  inner: function () {
    (() => {
      console.log(this);
    })();
  },
  outer: function () {
    arrow_outer();
  },
};

obj.inner(); // obj
obj.outer(); // window
```

- arrow_outer는 이미 외부에서 정의될 때 this는 window를 가리키기 때문에 window가 출련된 것이고,
- obj 객체 내부에서 화살표 함수를 사용하면 this = obj를 가리키던 것과는 차이를 보입니다.

### “arguments” binding

- Arguments 객체는 화살표 함수에서 사용할 수 없지만 일반 함수에서는 사용할 수 있다.

```jsx
let myFunc = {
  showArgs() {
    console.log(arguments);
  },
};
myFunc.showArgs(1, 2, 3, 4);
```

## ✔️ 참고

[[ES6] 화살표 함수 (Arrow Function) 파헤치기](https://paperblock.tistory.com/51)

[Arrow function | PoiemaWeb](https://poiemaweb.com/es6-arrow-function)
