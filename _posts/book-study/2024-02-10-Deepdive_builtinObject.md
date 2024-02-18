---
title: "JavaScript Deepdive - Built-in Object"
categories:
  - book-study
tags:
  - js-deepdive
toc: true
toc_sticky: true
---

## 자바스크립트 객체의 분류

> 자바스크립트 객체는 다음과 같이 크게 3개의 객체로 분류할 수 있다.

- 표준 빌트인 객체
  - ECMAScript 사양에 정의된 객체를 말하며, app 전역의 공통 기능을 제공한다.
    - [ECMAScript와 JavaScript는 무슨 차이점이 있을까?](https://wormwlrm.github.io/2018/10/03/What-is-the-difference-between-javascript-and-ecmascript.html)
  - 전역 객체의 프로퍼티로서 제공되기 때문에, 별도의 선언 없이 전역 변수처럼 언제나 참조 가능.
- 호스트 객체
  - ECMAScript 사양에 정의되어 있지않지만, JS 실행환경(브라우저 or Node.js)에서 추가로 제공하는 객체.
  - 브라우저 제공 객체
    - DOM, BOM, Canvas, XMLHttpRequest, fetch, requestAnimationFrame, SVG 등의 클라이언트 사이드 Web API를 호스트 객체로 제공하고, Node.js 환경에서는 Node.js 고유의 API를 호스트 객체로 제공한다.
- 사용자 정의 객체

  - 용어 그대로 사용자가 직접 정의한 객체.

## 표준 빌트인 객체

- Object, String, Number, Boolean, Symbol, Date, Math, RegExp, Array, Map/Set, Function, Promise, Proxy, JSON 등 40여개의 표준 빌트인 객체를 제공한다.
- Math, Reflect, JSON -> 비 생성자 함수. 인스턴스 생성 불가능. 정적 메서드만 제공.
- 이외의 표준 빌트인 객체 -> 생성자 함수. 인스턴스 생성 가능. 정적 메소드 및 프로토타입 메서드 제공.

```js
const strObj = new String('Uber'); // String {"Uber"}
console.log(typeof strObj); // object

const funObj = new Function('x', 'return x * x'); f annoymous(x)
console.log(typeof funObj); // function
```

- 각 생성자 함수를 통해 생성된 인스턴스의 프로토타입은 각 생성자 객체의 prototype 프로퍼티에 바인딩된 객체다.

```js
const strObj = new String("Lee");
console.log(Object.getPrototypeOf(strObj) === String.prototype); // true
```

## 원시값과 래퍼 객체

> 문자열이나 숫자, 불리언 등의 원시값이 있는데도 String, Number, Boolean 등의 표준 빌트인 생성자 함수가 존재하는 이유는?

- 원시값은 객체가 아니므로 프로퍼티나 메서드를 가질 수 없는데도, 아래에서 원시값은 마치 객체처럼 동작한다.

```js
const str = "hello";

console.log(str.length); // 5
```

- 이는 원시값인 문자열, 숫자, 불리언 값을 객체처럼 마침표 표기법으로 접근 시, 자바스크립트 엔진이 일시적으로 원시값과 연관된 객체를 생성하여 이 객체로 변환해서 해당 함수를 호출한뒤, 다시 원시값으로 되돌리기 때문이다.

- 이처럼 원시값에 객체처럼 접근하면 일시적으로 생성되는 임시 객체를 래퍼 객체(Wrapper object) 라고 한다.

```js
// str.name이 undefined인 이유

const str = "example"; // 1
str.name = "name"; // 2

console.log(str.name); // undefined // 3
```

- 1에서 원시값 선언 및 초기화가 이루어진다.
- 2에서 마침표 표기법으로 name에 접근하면, 'example' 값과 연관된 객체(String)를 일시적으로 생성하여 str.name에 접근하게 해준다.

- 이 때 기존의 원시값은 래퍼객체의 [[StringData]] 내부 슬롯에 할당된다.

- 3에서 다시 str.name에 접근하면, 이 때 새롭게 생성되는 일시적 래퍼 객체는 2에서 생성된 래퍼객체와 다른 객체이다. (2에서 생성된 래퍼객체는 더이상 호출되지 않으므로 즉시 GC에 의해 수집됨.)

- 때문에 3에서 str.name은 undefined 상태가 된다.

### String외에 Number, Boolean도 동일한 원리로 동작하며, 생성자 함수를 통해 인스턴스를 생성하지 않아도 객체처럼 사용할 수 있다. 때문에 String, Number, Boolean은 인스턴스 생성이 권장되지 않는다.

## 전역 객체

> 전역 객체는 코드가 실행되기 이전 단계에 JS 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않은 최상위 객체다.

- 이 전역 객체는 환경에 따라 지칭하는 이름이 제각각이다. 브라우저 환경에서는 window, self, this, frames / Node.js 환경에서는 global이 전역 객체를 가리킨다.

- ES11에서는 이를 통일하기 위해 globalThis를 표준 사양으로 지정했다.

```js
globalThis === window; // true
globalThis === this; // true
globalThis === global; // true - Node.js
globalThis === this; // true - Node.js
```

- 전역 객체는 표준 빌트인 객체(Object, String, Number 등)와 환경에 따른 호스트 객체(Client Web API or Node.js 호스트 API), 그리고 var 키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 갖는다.

- 전역 객체는 계층적 구조상 어떤 객체에도 속하지 않은 모든 빌트인 객체(표준 빌트인 객체와 호스트 객체)의 최상위 객체다.
- 하지만 이는 프로토타입 상속 관계 상 최상위 객체라는 의미는 아니다. 전역 객체 자신은 어떤 객체의 프로퍼티도 아니며, 객체의 계층적 구조상 표준 빌트인 객체와 호스트 객체를 프로퍼티로 소유한다는 것을 말한다.

```js
var str = "example";
console.log(window.str); // example
```

### 전역 객체의 특징은 다음과 같다.

- 개발자가 의도적으로 생성할 수 없다. 즉, 전역 객체를 생성할 수 있는 생성자 함수가 제공되지 않는다.
- 전역 객체의 프로퍼티를 참조할 때, window(or global) 생략 가능.

```js
// 문자열 F를 16진수로 해석하여 10진수로 변환하여 반환한다.
window.parseInt("F", 16); // 15

// 전역 객체의 프로퍼티는 window 생략 가능.
parseInt("F", 16); // 15

window.parseInt === parseInt; // true
```

- 전역 객체는 모든 표준 빌트인 객체를 프로퍼티로 가지고 있다.
- 자바 스크립트 실행 환경(브라우자 or Node.js)에 따라 추가적으로 프로퍼티와 메서드를 갖는다.
- var 키워드로 선언한 전역 변수와 선언하지 않은 변수에 값을 할당한 암묵적 전역, 그리고 전역 함수는 객체의 프로퍼티가 된다.

```js
var foo = 1;
console.log(window.foo); // 1

bar = 2;
console.log(window.bar); // 2

function baz() {
  return 3;
}
console.log(baz()); // 3
```

- let, const는 전역 객체 프로퍼티가 아니다. 이들은 보이지 않는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드) 내에 존재하게 된다.

- 브라우저 환경의 모든 JS 코드는 하나의 전역 객체 window를 공유한다. 여러개의 script 태그를 통해 JS 코드를 분리해도 마찬가지.

## 빌트인 전역 프로퍼티

### Infinity

- 무한대를 나타내는 숫자값.

### NaN

- 숫자가 아님을 나타내는 NaN을 갖는다. NaN === Number.NaN

### undifined

## 빌트인 전역 함수

- app 전역에서 호출할 수 있는 빌트인 함수로서 전역 객체의 메서드다.

### eval

- 안티패턴. 사용 안하는걸 권장.

### isFinite

- 인수의 유한수 / 무한수 여부 검증 및 boolean 반환.

### isNaN

### parseFloat, parseInt

### encodeURI, decodeURI

- encodeURI 함수는 완전한 URI를 문자열로 전달받아 이스케이프 처리를 위해 인코딩 하며, decodeURI는 이스케이프 처리 이전으로 디코딩 한다.
- 인코딩 : URI의 문자들을 이스케이프 처리하는 것.
  - 이스케이프 : 네트워크를 통해 정보 공유 시 어떤 시스템에서도 해독 가능한 아스키 문자 셋으로 변환하는것.
- URI 문법 형식 표준에 따르면 URL은 아스키 문자 셋으로만 구성 되어야 하며, 한글을 포함한 대부분의 외국어나 아스키 문자 셋에 정의되지 않은 특수문자의 경우 URL에 포함될 수 없다.

```js
const uri = "http://example.com?name=위버&job=programmer&teacher";

console.log(encodeURI(uri));
// 'http://example.com?name=%EC%9C%84%EB%B2%84&job=programmer&teacher'
```

### encodeURIComponent, decodeURIComponent

- URI 구성요소를 인수로 전달받아 인코딩 한다. 이 때, 문자열을 URI의 구성요소인 쿼리 스트링의 일부로 간주하여 쿼리 스트링 구분자(&, ?, =)는 인코딩 하지 않는다.

```js
const uriComp = "name=위버&job=programmer&teacher";

console.log(encodeURIComponent(uriComp));
// "name%3D%EC%9C%84%EB%B2%84%26job%3Dprogrammer%26teacher"
```

## 암묵적 전역

```js
var x = 10; // 전역 변수

function foo() {
  y = 20;
}
foo();

console.log(x + y); // 30;
```

- y는 선언하지 않은 식별자지만, JS 엔진은 y를 window.y로 해석하여 전역 객체에 프로퍼티를 동적으로 생성한다. 이 현상을 암묵적 전역이라 한다.
- 하지만 단지 프로퍼티로 추가되었을 뿐이며, 변수가 아니므로 호이스팅 대상 x
