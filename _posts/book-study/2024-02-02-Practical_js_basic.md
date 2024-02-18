---
title: "실용적인 자바스크립트 - Basic"
categories:
  - book-study
tags:
  - practical-js
toc: true
toc_sticky: true
---

### 폴리필(Polyfill)

- 해당 기능을 지원할 수 없거나, 호환성 문제로 인해 같은 기능이 다르게 실행될 때, 기능을 사용할 수 있도록 대체 구현을 하는 코드, 또는 라이브러리를 말한다.

### Promise

- 자바스크립트의 비동기 객체 (Promise)를 한글로 사용하면서 혼용 하고 있음.

### Fetch

- 자바스크립트의 비동기 통신 기능. 상기 Promise를 보다 쉽게 사용할 수 있도록 제공되는 프로미스의 Wrapper 기능.

### 동기(Sync) 와 비동기(Async)

- 동기는 코드가 순차적으로 실행되는것. 비동기는 앞쪽에 나오는 코드가 나중에 실행될 수 있는것. JS는 Promise와 같은 비동기 내장 객체를 지원한다.

### 스코프(Scope)

- 변수, 또는 코드가 영향을 미치는범위.

### 전역(Global), 함수(Function), 블록(Block), 로컬 스코프(Local Scope)

- var는 전역과 함수 스코프를 지원하며, let, const는 전역과 블록 스코프를 지원한다.
- 함수 스코프는 함수 안에서만 접근이 가능한 지역변수가 되며, 블록 스코프는 블록 안에서만 접근이 가능하다.
- let, const는 보다 정밀한 스코프 제어가 가능하고, 덕분에 리소스 낭비가 적다.
- 로컬 스코프는 전역이 아닌 함수, 블록 스코프를 말한다.

### 순회(Traversal)와 순환(Rotation)

- JS의 순회란 방문해야 할 지점을 한번씩 방문하고 종료하는것. (ex. DOM 탐색 등)
- 순환이란 순회를 계속 반복하는것.

### 호이스팅(Hoisting)

- 변수와 함수 선언을 포함한 모든 선언을 스코프 최상단으로 끌어올려 스코프 안의 모든 코드에서 선언 정보에 접근이 가능하도록 하는 JS의 주요 기능.
- var, let, const, function, class와 같은 선언이 호이스팅 대상.

> 호이스팅 예시와 호이스팅 과정

```js
a = 0;
var a = 10;
console.log(a);

// 1. var a = undefined;
// 2. a = 0;
// 3. a = 10;
```

### 생성자 함수(Constructor Function)

- 반드시 "new" 연산자를 사용해 호출하며, 함수 이름 첫글자는 대문자로 시작.
- 생성자 함수와 일반 함수 사이에 기술적인 차이는 없다.
- "new" 연산자를 써서 함수를 실행하면 아래와 같은 알고리즘이 동작한다.
  - 빈 객체를 만들어 this에 할당합니다.
  - 함수 본문을 실행합니다. this에 새로운 프로퍼티를 추가해 this를 수정합니다.
  - this를 반환한다.

```js
function Cellphone(brand, price) {
  this.brand = brand;
  this.price = price;
}

var iPhone = new Cellphone("apple", 12000000);
var galaxy = new Cellphone("samsung", 12000000);

//

var iphone = {
  brand: "apple",
  price: 12000000,
};

var galaxy = {
  brand: "samsung",
  price: 12000000,
};
```

### 제너레이터 함수(Generator function)

- 생성자 함수와 혼동하기 쉽지만, 전혀 다름.
- 함수를 여러번 반복하며 순차적으로 반복적인 값을 얻을 수 있는 특별한 함수.
- function* 혹은 함수이름앞에 *를 붙임. 보통 전자의 경우를 많이 사용함.

```js
function* generator() {
  yield 1;
  yield 2;
  yield 3;
  return 4;
}

let init = generator();
let one = init.next();
let two = init.next();

console.log(one); // 1
console.log(tow); // 2
```

### 클로저 함수(Closure function)

- 실행 시점, 또는 호출 시점의 렉시컬 환경(Lexical Environment)을 호출된 함수에서 기억하는 것을 클로저라고 한다.
- 렉시컬 환경이란, 현재 실행 context의 스코프에 있는 선언 정보와 값을 말한다.

```html
<button id="btn1">버튼1</button>
<button id="btn2">버튼2</button>
<button id="btn3">버튼3</button>

<script>
  function count() {
    var cnt = 0;
    return {
      plusCnt: function () {
        cnt++;
        console.log(cnt);
      },
    };
  }

  var countIIFE = (function () {
    var cnt = 0;
    return {
      function() {
        cnt++;
        console.log(cnt);
      },
    };
  })();

  let count1 = new count();
  let count2 = new count();
  let count3 = countIIFE;

  document.getElementById("btn1").addEventListener("click", count1.plusCnt);
  document.getElementById("btn2").addEventListener("click", count2.plusCnt);
  document.getElementById("btn3").addEventListener("click", count3);
</script>
```

### 클로저 기초

```js
function outerFunc(name) {
  let saying = name + "안녕!";
  return function () {
    return saying;
  };
}

let closure1 = outerFunc("라이언");
let closure2 = outerFunc("콘");

console.log(closure1); // 라이언 안녕!
console.log(closure2); // 콘 안녕!
```

- outerFunc() 함수는 익명 함수를 반환하고 종료되며, 로컬변수 saying도 사라져야 한다.
- 하지만 closure1, closure2를 실행하면 이미 종료된 부모함수 outerFunc의 saying 변수에 접근 가능.
- 자식 함수에 해당하는 익명 함수가 부모 함수인 outerFunc() 함수의 실행 환경에 속한 saying 변수에 부모 함수 종료 후 접근할 수 있는것을 클로저 라고 하며, 익명 함수는 클로저 함수가 된다.

### var, let, const의 차이

| 선언자 | 중복선언 | 재할당 | 초기값    | 스코프 |
| ------ | -------- | ------ | --------- | ------ |
| var    | 가능     | 가능   | undefined | 함수   |
| let    | 불가능   | 가능   | undefined | 블록   |
| const  | 불가능   | 불가능 | 불가능    | 블록   |

### 절대값, 숫자의 부호를 얻기

- Math.abs() -> 부호 없는 양의 숫자 값을 반환. (절대값)
- Math.sign() -> 숫자의 부호를 알려줌. 1, 0, -1, NaN 중 1개 반환

### true/false의 판단

- false, null, undefined, '', 0, NaN 모두 false로 판단.

```js
if (data) {
  console.log(data);
}
```

### null, undefined, 0의 차이

- JS에서 값이 할당되지 않은것을 표현하는 방법은 2가지.
  - Null. 변수가 빈 값으로 초기화 된 상태.
    - 사용자가 명시적으로 변수에 대입 하는 경우 외에는 발생하지 않음.
  - undefined. 사용자에 의해 변수에 값이 대입되지 않은 초기 상태의 변수.
    - 선언은 되었지만 초기화는 되지 않은 상태.
    - ex) let a;
    - 이와 관련해서, 함수 정의 시 불필요하게 많은 파라미터를 정의하는 것은 굉장히 좋지 않은 결과를 낳을 수 있음.
    -

### 인자(Argument)와 파라미터(Parameter)의 차이

- 파라미터는 함수 정의 시 나열하는 변수명. 인자는 함수를 호출할 때 전달하는 실제 값.

### 객체(Object)

- JS의 객체는 다양한 종류의 데이터들을 하나의 저장소에 담아 접근 및 관리하기 위해 사용.

```js
var example = { company: "uber", depart: "dev", floor: 2 };
```

- 함수 자체를 객체에 저장할 수도 있음.

```js
var example = {
    var say = 'Hello!';
    sayHello : function() {
        console.log(say);
    }
}
```

- 컨벤션
  - key, value 사이에 공백을 하나 둔다.
  - 객체 요소 구분자 쉼표 뒤에는 공백을 하나 둔다.
  - 문자열 값 표현은 쌍따옴표를 사용하는것을 권장한다.

* 하나의 객체 안에는 하나의 키만 존재. but 하위 객체에 중복 key 선언 가능.

```js
var example = { company: "uber", depart: "dev", depart: "uid" }; // error
var example1 = { company: "uber", depart: "dev", person: { depart: "uid" } };
```

### 숫자를 문자열로 바꾸기

- 숫자 앞, 뒤에 빈 문자열 ''을 + 연산자로 더해주면 숫자가 문자로 자동캐스팅 됨.

```js
var str = 1 + "";
```

### 숫자 구분자 허용

```js
var num = 1_000_000_000; // equals 1000000000
```

### 객체 유효성 체크 - 옵셔널 체이닝 연산자 '?'

```js
var obj = {
  company: {
    name : 'the uber creative',
    addr : 'nonhyeon',
  }
  depart: "dev",
};

if(obj.company && obj.company.name) {} //
if(obj.company?.name) {} //
```

### TDZ(Temporary Dead Zone)

- 임시 사각 지대. 호이스팅 된 변수, 함수 선언 정보 중 초기화 안된것들을 따로 저장해두는 분리된 메모리 영역.
- 렉시컬 환경 : 현재 실행중인 코드의 스코프 안에 선언된 변수, 함수정보와 값을 보관하는 메모리 영역
- 환경 레코드 : 현재 실행중인 코드 행의 스코프 안에 선언된 변수와 함수의 실행 환경 정보를 저장한 곳

### 함수 선언과 함수 표현의 호이스팅

- 아래 두 방법 모두 표준 JS 함수 정의 방법.

```js
var a = function () {
  console.log("example1");
}; // 함수 표현

function b() {
  conosle.log("exmaple2");
} // 함수 선언
```

- 아래의 a()는 error 발생, b()는 정상작동 이유

```js
a();
var a = function a() {
  console.log("a");
};

b();
function b() {
  console.log("b");
}
```

> 호이스팅 순서 때문.

```js
var a = undefined; // 1. 변수 호이스팅
function b() {
  console.log("b"); // 2. 함수 호이스팅
}
a(); // 3. 함수 호출 - error
a = function a() {
  console.log("a"); // 4. 함수 정의
};
```

### 가비지 콜렉션(Garbage Collection)

- 메모리 관리 방식으로, 할당 후 사용되지 않는 메모리 공간을 일정 주기로 검사해서 회수하는 메모리 관리 방식.
- 더이상 변수에 의해 참조되지 않는 메모리 영역을 해제하는 방식으로 GC 작동.

### debugger 키워드로 디버깅 시작

- debugger 키워드를 코드 특정 위치에 삽입하면, 브라우저는 코드 실행을 해당 위치에서 잠시 멈추고 내장 디버거를 활성화 시킴.

```js
let a = 100;
let b = 200;
let c = a + b;
console.log("c=", c);

debugger;

let d = a - b;
console.log("d=", d);
```
