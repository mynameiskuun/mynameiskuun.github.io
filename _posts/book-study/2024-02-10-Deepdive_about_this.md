---
title: "JavaScript Deepdive - this"
categories:
  - book-study
tags:
  - js-deepdive
toc: true
toc_sticky: true
---

## this 키워드

- 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 특수한 식별자를 this라고 하며, 이는 인스턴스 식별자를 알 수 없을 때 객체 내부의 프로퍼티와 메소드를 참조하기 위해서 만들어졌다.

### 객체 리터럴 방식

- 아래와 같은 객체 리터럴 방식의 객체에서는 getDiameter() 메서드가 속한 circle를 가리키는 식별자를 재귀적으로 참조할 수 있다.

```js
const circle = {
    radius = 5;

    getDiameter() {
        return 2 * circle.radius;
    }
};

console.log(circle.getDiameter()); // 10
```

- 하지만 자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 권장되지 않는다.

### 생성자 함수 방식

```js
function Circle(radius) {
    // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
    ???.radius = radius;
}

Circle.prototype.getDIameter = function() {
    // 마찬가지로 식별자를 알 수 없다.
    return 2 * ????.radius;
}

// 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수를 정의해야 한다.
const circle = new Circle(5);
```

- 생성자 함수를 통해 인스턴스를 생성하려면, 먼저 생성자 함수가 존재해야 한다.
- 하지만 이 생성자 함수에서 프로퍼티, 메서드 추가를 위해서는 자신이 생성할 인스턴스를 참조할 수 있어야 한다.

- 이 모순을 해결하기 위해 this라는 특수한 식별자를 제공하여, 생성자 함수 내부에서 인스턴스를 생성하기 전에 인스턴스를 참조할 수 있다.

- 단, this가 가리키는 값, this 바인딩은 함수 호출 방식에 의해 동적으로 결정된다.

```js
console.log(this); // this : window

function ex() {
  console.log(this); // this : window
}

const person = {
  name: "Lee",
  getName() {
    console.log(this); // this : {name: "lee", getName: f}
    return this.name;
  },
};

function Person(name) {
  this.name = name;
  console.log(this); // this : Person {name: "lee"}
}

const me = new Person("lee");
```

- 이 this는 객체의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수이므로, 일반적으로 객체의 메서드 내부 또는 생성자 함수 내부에서만 의미가 있다.

## 함수 호출 방식과 this 바인딩

- this 바인딩은 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.
- 렉시컬 스코프와 this 바인딩은 결정 시기가 다르다.
  - 렉시컬 스코프 : 함수의 상위 스코프를 결정하는 방식. 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정한다.
  - this 바인딩 : 함수 호출 시점에 결정된다.

```js
const foo = function () {
  console.log(this);
};

foo(); // window

const obj = { foo };
obj.foo(); // obj

new foo(); // foo {} (instance)
```

### 일반 함수 호출

- 기본적으로 this에는 전역 객체가 바인딩 된다.

```js
function foo() {
  console.log("foo's this : ", this); // window
  function bar() {
    console.log("bar's this : ", this); // window
  }
  bar();
}
foo();
```

- 전역 함수는 물론이고 중첩 함수를 일반 함수로 호출하면 함수 내부의 this에는 전역 객체가 바인딩된다.
- this는 객체 내부의 프로퍼티와 메소드를 참조하기 위한 자기 참조 변수 이므로, 객체를 생성하지 않는 일반 함수에서 this는 의미가 없다. 따라서 strict mode 적용 시 이 경우에는 this에 undefined가 바인딩 된다.

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this : ", this); // { value : 100, foo: f}
    console.log("foo's this.value: ", this.value); // 100;

    function bar() {
      console.log("bar's this : ", this); // window
      console.log("bar's this.value: ", this.value); // 1
    }
    bar();
    // 메서드 내에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는 전역 객체가 바인딩 된다.
  },
};

obj.foo();
```

- 콜백 함수가 일반 함수로 호출되면 콜백 함수의 내부 this에도 전역 객체가 바인딩 된다.
- 어떠한 함수라도 일반 함수로 호출되면 this에 전역 객체가 바인딩 된다.

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // {value : 1, foo : f}
    setTimeout(function () {
      console.log("callback's this : ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  },
};

obj.foo();
```

- 콜백 함수는 보통 외부 함수를 돕는 헬퍼 함수의 역할을 하는데, 위의 예시처럼 외부함수와 콜백함수의 this가 일치하지 않는다는 것은 중첩 함수 또는 콜백 함수를 헬퍼 함수로 동작하기 어렵게 만든다.
- 이 두 this 바인딩을 일치시키기 위해 다음의 방법을 사용할 수 있다.

```js
// 일치용 변수 that 사용
const obj = {
  value: 100,
  foo() {
    const that = this;
    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  },
};

// bind 함수를 이용한 명시적 this 바인딩
var value = 1;
const obj = {
  value: 100,
  foo() {
    setTimeout(
      function () {
        console.log(this.balue); // 100
      }.bind(this),
      100
    ); // 콜백 함수에 명시적으로 this를 바인딩 한다.
  },
};

// 화살표 함수를 사용한 this 바인딩
var value = 1;
const obj = {
  value: 100,
  foo() {
    setTimeout(() => console.log(this.value), 100); // 100
  },
};
obj.foo();
```

### 메서드 호출

- 메서드 내부의 this는 메서드를 소유한 객체가 아닌, 메서드 자신을 호출한 객체에 바인딩 된다. (???.method() -> method에 바인딩)

```js
const employee = {
  depart: "Dev",
  getDepart: function () {
    return this.depart;
  },
};

console.log(employee.getDepart()); // Dev
```

- 위 예제의 getDepart 프로퍼티는 함수 객체를 가리키고 있을 뿐이며, 이 가리키는 함수 객체는 employee 객체에 포함된 것이 아닌 독립적으로 존재하는 별도의 객체다.

```js
const employee_2 = {
    depart: "UID";
}

employee_2.getDepart = employee.getDepart;

console.log(employee.getDepart()); // UID

const getDepart = employee.getDepart;

console.log(getDepart()); // ''
// 일반 함수로 호출된 getDepart 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
```

- 위의 예시와 같이, getDepart 프로퍼티가 가리키는 함수 객체, 즉 getDepart 메서드는 다른 객체의 프로퍼티에 할당하는 것으로 다른 객체의 메서드가 될 수도 있고 일반 변수에 할당하여 일반 함수로 호출될 수도 있다.

```js
function SoccerPlayer(position) {
  this.position = position;
}

SoccerPlayer.prototype.getPosition = function () {
  return this.position;
};

const son = new SoccerPlayer("Forward");
console.log(son.getPosition()); // Forward

SoccerPlayer.prototype.position = "Midfilder";

console.log(SoccerPlayer.prototype.getPosition()); // Midfilder
```

### 생성자 함수 호출

- 생성자 함수 내부의 this에는 생섬자 함수가 (미래에) 생성할 인스턴스가 바인딩 된다.

```js
function incomeTax(salary) {
  this.salary = salary;
  this.getIncomeTax = function () {
    return salary * 0.1;
  };
}

const tax1 = new incomeTax(20);
const tax2 = new incomeTax(50);

console.log(tax1.getIncomeTax()); // 2
console.log(tax2.getIncomeTax()); // 5
```

### Function.prototype.apply/call/bind 메서드에 의한 간접 호출

- [prototype.apply,call,bind Referance](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

- apply, call 메서드의 기본 기능은 이 메서드가 붙은 함수를 호출하는 것이다.
- 일반 호출과 다른점은, 호출한 함수의 this에 apply, call에 담기는 인자(객체)를 바인딩 한다는 것이다.

```js
function example() {
  console.log("this : ", this);
  console.log("arguments : ", arguments);
}

const obj = {
  name: "example",
};

console.log(example.apply(obj, ["arg1", "arg2"])); // this : {name : 'example'}
console.log(example.call(obj, "arg1", "arg2")); // arguments : ['arg1', 'arg2'];
```

- Function.prototype.bind 메서드는 apply, call 메서드와 다르게 함수를 호출하지 않는다. 다만 인수로 전달한 값을 호출된 함수의 this에 바인딩 해 반환한다.
- bind 메서드는 메서드의 this와 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 사용된다.

```js
const ex = {
  name: "innerName",
  getName(callback) {
    setTimeout(callback, 100);
  },
};

ex.getName(function () {
  console.log(`'Hello! my name is ${this.name} !'`); // 'Hello! my name is !'
});
```

- 객체.메소드() 의 경우 메소드의 this는 호출된 객체를 가리킨다. 때문에 ex.getName 메소드 실행 시 this.name에 innerName이 바인딩 되어야 할것 같지만
- setTimeout의 콜백 함수가 일반 함수로서 호출된 시점에는 this에는 전역 객체 window가 바인딩 된다.
- callback 함수는 getName을 돕는 헬퍼 함수 역할을 하지만, 내외부의 this가 불일치 하여 문맥상 문제가 발생한다. 이 this를 일치 시키기 위해 bind 메서드를 사용할 수 있다.

```js
const ex = {
  name: "innerName",
  getName(callback) {
    setTimeout(callback.bind(this), 100);
  },
};

ex.getName(function () {
  console.log(`'Hello! my name is ${this.name} !'`); // 'Hello! my name is innerName !';
});
```

- bind 메서드는 인자로 받은 값을 호출된 함수의 this에 바인딩 시키고 그 함수를 리턴한다.
- 위의 예시와는 다르게 bind 메소드를 통해 강제로 getName 메서드 내부에서의 this를 바인딩 시켰기 때문에, 독립적으로 콜백 함수가 일반 함수로서 실행 되더라도 언제나 this에는 ex 객체가 바인딩 된다.
