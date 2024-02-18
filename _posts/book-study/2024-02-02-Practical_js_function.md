---
title: "실용적인 자바스크립트 - Function"
categories:
  - book-study
tags:
  - practical-js
toc: true
toc_sticky: true
---

### Rest Parameter(나머지 파라미터)

- 함수의 마지막 파라미터에 마침표 3개 (...)를 붙여 사실상 무한대의 파라미터를 사용할 수 있게 합니다.

```js
function example(a, b, ....C) => {
 console.log(a,b,C)
}
```

- 위의 경우, C 가변인자는 무한대의 파라미터를 받을 수 있기 때문에 배열로 출력 됩니다.

---

### Arrow Function(화살표 함수)

- 자바의 람다 표현식과 유사한 것으로, 예시를 통해 쉽게 이해할 수 있습니다.

```js
function(a,b) {
    return a + b;
}

화살표 함수 적용

var example = (a,b) => {
    return a + b;
}

```

#### 화살표 함수의 주요 특징

- 함수 안에 super, this 바인딩이 없음.
- new 키워드 생성자를 사용할 수 있음.
- 블록 스코프를 사용.

> 화살표 함수 사용 시 유의사항

- => 화살표는 항상 변수 선언부와 동일한 row에 위치해야 합니다.

---

### 일급 함수, 고차 함수

> 일급 함수란, 다른 함수의 매개변수로 삽입되거나 return의 직접적인 대상이 될 수 있는 함수를 말합니다.

---

### 변수의 스코프와 스코프 체인

> 스코프(scope)란 ? 현재 실행 코드가 접근할 수 있는 선언되 변수의 범위를 말합니다. 반대로 말하면 변수가 영향을 미치는 코드의 범위입니다.

- 자바스크립트의 3개의 스코프 타입은 각각 다른 스코프를 사용합니다.

| 타입  |   스코프    |
| :---: | :---------: |
|  var  | 함수 스코프 |
|  let  | 블록 스코프 |
| const | 블록 스코프 |

- 함수 스코프 체인의 대표적인 예시

```js var a = 1; var b = 5;
function outerFunc() {
  function innerFunc() {
    a = b;
  }
  console.log(a); //1
  a = 3;
  b = 4;
  innerFunc();
  console.log(a); //4
  var b = 2;
}
outerFunc();
console.log(a, b); //4,5
```

### 함수 선언과 함수 표현

- 함수 선언 예시

```js
function example(a, b) {
  console.log(a);
  console.log(b);
}
```

- 함수 표현 예시

```js
let example = function funcName() {
  return "example";
};
```

---

### 함수 표현과 함수 선언의 착각

```js
let a = function nameFunc() {
  console.log("nameFunc!");
};
```

> 위의 경우는 함수 표현입니다.

- 함수 선언 몸체에 함수 이름이 있어도(nameFunc()) 변수에 대입을 하면(a) 함수 표현으로 선언됩니다.
- 즉, nameFunc()은 함수가 아니기 때문에 사용할 수 없고, 함수 표현 변수명으로 호출해야(a()) 함수가 실행됩니다.

---

### 함수 표현과 클로저

- 호이스팅이 지원되는 함수 선언을 사용하는것이 언뜻 보면 더 좋아보이지만, 이로 인해 여러개의 함수들이 호이스팅 되면 함수의 스코프 이슈를 피하기가 함들어집니다.
- 이와 더불어 함수 표현을 사용하는 이유는 함수 표현이 클로저를 지원하기 때문입니다.

```js
var buttons = document.querySelectorAll(".button");

let i;
for(i=0; >buttons.length;, i++) {
    buttons[i].onclick = (e) => {console.log("index" + i)};
}
```

- 위의 코드에서, i의 값에 따라 각각 1,2,3이 출력되야 할것 같지만, 루프문이 모두 실행된 후의 값인 i=3으로 모두 출력됩니다.
- 버튼 클릭 시, 각각의 i를 출력시키려면 아래의 함수 표현(클로저 특성을 지닌)을 사용해야 합니다.

```js
var buttons = document.querySelectorAll('.button');

let clickHandler = (index) => {
    return => {
        console.log("index : " + index);
    }
}

let i;
for(i = 0; i < buttons.length; i++) {
    button[i].onclick = clickHandler(i);
}
```

### 인터페이스 함수

- 자바스크립트의 객체 작성 기법중 하나.
- 한 개 이상의 함수를 함수로 다시 감싸서 객체, 정확히는 객체 리터럴을 반환하는 래퍼(Wrapper) 함수의 역할을 하는 함수를 말합니다.
- return으로 함수 안에 정의한 함수들의 이름을 담은 객체 리터럴을 반환한다.

```js
function math(a, b) {
  function plus() {
    return a + b;
  }
  function minus() {
    return a - b;
  }
  function multiple() {
    return a * b;
  }
  function divide() {
    return a / b;
  }

  return { plus, minus, multiple, divide };
}

const math = math(100, 50);
console.log(math.plus()); // 150
console.log(math.minus()); // 50
```

### 커링(Currying) 함수

- 단일 호출로 처리하는 여러 파라미터를 가진 함수를 단일 파라미터를 사용하는 중첩 함수로 분리해서 사용하는 기법.

```js
function add(a, b) {
  return a + b;
}
add(1, 2);
```

> Currying ver

```js
function add(a) {
  return function (b) {
    return a + b;
  };
}

add(1)(2);
```

- 부분 실행하는 단계를 분리 해 코드를 나눌 수 있지만, 파라미터 개수가 많고 중첩이 깊어지면 콜 스택 유지에 메모리를 더 사용하여 실행 속도 측면에서 불리해짐. 또한 중첩이 깊어지면 가독성 하락.

- 화살표 함수를 이용해 짧은 코드로도 작성 가능.

```js
sum = (num1) => (num2) => (num3) => num1 + num2 + num3;
```

### 재귀 커링

```js
function sum(a) {
  return function (b) {
    if (b === undefined) {
      return a;
    } else {
      return sum(a + b);
    }
  };
}

console.log(sum(1)()); // 1
console.log(sum(1)(2)()); // 3
```

- 마지막 고차함수를 즉시실행 시키기 위해 빈 괄호를 넣으며, 재귀 커링 함수는 반드시 이렇게 구현해야 함.

### 믹스인 함수

- 단일 객체 상속의 한계를 넘어, 여러 객체에서 메소드나 속성을 상속받을 수 있게 하는 함수.
- 객체의 메소드, 속성을 모두 대상 객체로 복사 하며, 이름이 중복되면 값을 덮어씀.
- jQuery의 $.extend 메소드와 유사한 기능.

```js
function mixin(sourceObj, targetObj) {
  return Object.assign(sourceObj, targetObj);
}

var a = { name: "sam", age: 20 };
var b = { age: 21, addr: "seoul" };

var c = mixin(a, b);
console.log(c); // {name : "sam", age : 21, addr : "seoul"};
```

### 디바운스 함수

- 디바운싱 이란, 동일 함수의 잦은 반복 실행을 차단하고 마지막 호출한 것만 실행되도록 하는 코딩 패턴.
  - 검색어 입력 시, 매 키보드 입력 마다 api 호출이 된다면?

```js

function debounceFunc(callback, delay=100) {
  let timer;

  return function() { // 디바운스 함수
    if(timer) clearTimeout(timer);
    timer = setTimeout{callback, delay}
  }
}

function runner() { // 콜백 함수
  console.log(new Date());
}

let df = debounceFunc(runner, 1000);
```
