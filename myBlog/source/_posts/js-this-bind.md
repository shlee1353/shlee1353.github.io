---
title: 자바스크립트(JavaScript) this 바인딩 기본개념
date: 2019-07-04 16:54:24
tags:
---

![](/image/js-this-bind/1.png)

자바스크립트에서 this 키워드는 좀 특별합니다. ES5에서는 bind 메소드를 사용해서 this 키워드를 설정 할 수 있으며, ES2015에서는 arrow functions 을 사용해서 고유의 this 키워드를바인딩 할 수 있습니다. (렉시컬 컨텍스트 안에 this를 유지)

## Global context
함수 밖, 글로벌 실행 컨텍스트에서 this는 글로벌 객체를 가르킵니다(strict mode or not)

```js
console.log(this === window); // true
  
a = 37;
console.log(window.a); // 37
  
this.b = "MDN"; 
console.log(window.b)  // "MDN" 
console.log(b)         // "MDN"
```

## Function context
함수 내부에서 this는 함수 호출에 따라 달라집니다. 일반모드에서 함수 내부의 this는 글로벌을 가르키지만, strict mode에서는 undefined이 출력됩니다.

```js
// non-strict mode
function f1() {
  return this;
}
// In a browser:
f1() === window; // true 
// In Node:
f1() === global; // true
// strict mode
function f2() {
  'use strict'; // see strict mode
  return this;
}
f2() === undefined; // true
```

call, apply 메소드를 사용해서 this 값을 다른 컨텍스트로 전달 할 수 있습니다.

```js
var obj = {a: 'Custom'};
var a = 'Global';
function whatsThis(arg) {
  return this.a;
}
whatsThis();          // 'Global'
whatsThis.call(obj);  // 'Custom'
whatsThis.apply(obj); // 'Custom'
```

call과 apply의 차이점은 두 번째 파라미터에 배열값을 전달 할 수 있는가 없는가 입니다. 첫 번째로 전달되는 this는 객체로, 숫자나 문자열이 전달 되어도 ToObject로 인해 객체로 변환됩니다.

```js
function add(c, d) {
  return this.a + this.b + c + d;
}
var o = {a: 1, b: 3};
add.call(o, 5, 7); // 16
add.apply(o, [10, 20]); // 34
```

f.bind(someObject)은 f와 같은 바디와 스코프를 가진 새로운 함수를 생성합니다. 하지만. this는 본래 함수에서 발생합니다. bind는 중복 설정되지 않고 한 번만 가능합니다.

```js
function f() {
  return this.a;
}
var g = f.bind({a: 'azerty'});
console.log(g()); // azerty
var h = g.bind({a: 'yoo'}); // bind only works once!
console.log(h()); // azerty
var o = {a: 37, f: f, g: g, h: h};
console.log(o.f(), o.g(), o.h()); // 37, azerty, azerty
```

arrow 함수에서 this 는 렉시컬 컨텍스트와 연결됩니다. 글로벌 코드에서는 글로벌 객체와 연결됩니다. obj.bar 의 익명함수1 안에 arrow 함수로 선언된 익명함수2의 this는 익명함수1의 this와 연결되어 있고, 이 this는 다시 obj 객체와 연결되어 있습니다.

```js
// 예시 1
var globalObject = this;
var foo = (() => this);
console.log(foo() === globalObject); // true
// 예시 2
var obj = {bar: function() {
        var x = (() => this);
        return x;
      }
};
var fn = obj.bar();
console.log(fn() === obj); // true
```

함수가 객체의 메소드로 호출되면, 이것의 this는 메소드를 호출한 객체와 연결됩니다. o.f()가 호출되면, 함수 내부의 this는 o 객체가 된다.

```js
var o = {
  prop: 37,
  f: function() {
    return this.prop;
  }
};
console.log(o.f()); // 37
```

프로토타입 체인에서도 this는 함수가 호출된 객체에 지정이 됩니다.

```js
var o = {f: function() { return this.a + this.b; }};
var p = Object.create(o);
p.a = 1;
p.b = 4;
console.log(p.f()); // 5
```

수정된 프로그램의 영향이 전체 소스에 퍼지지 않도록 getter, setter 사용합니다. 예를들어

```js
//기존방법
var o = {};
o.num = 10;
// 개선방법
var o = {};
Object.defineProperty(o, 'num', {value:10});
```

defineProperty 함수에서 가장 중요한 것은 세번째 옵션으로 들어가는 인자입니다.

```js
value : 속성값
writable : 속성값 변경 여부
configurabl e: 속성의 옵션값 변경 여부
enumerable : for in 반복만 사용 가능 여부
get : value, writable 과 함꼐 사용 못함
set : value, writable 과 함꼐 사용 못함

var 원 = {};
원._반지름 = 0;   // 겟터 셋터 대상 및 초기값 지정
Object.defineProperty( 원, '반지름', { 
 get : function()   { return this._반지름; },
 set : function(값) { this._반지름 = 값;    }
});
// 사용예 
원.반지름 = 5;
document.write('원.반지름 = ', 원.반지름);
```

defineProperty없이 getter, setter 함수에 대해 알아보도록 하겠습니다. 아래처럼 person 객체에 get, set 함수를 정의해서 사용가능합니다.

```js
var person = {
    firstName: 'Jimmy',
    lastName: 'Smith',
    get fullName() {
        return this.firstName + ' ' + this.lastName;
    },
    set fullName (name) {
        var words = name.toString().split(' ');
        this.firstName = words[0] || '';
        this.lastName = words[1] || '';
    }
}
person.fullName = 'Jack Franklin';
console.log(person.firstName); // Jack
console.log(person.lastName) // Franklin
```

defineProperty에 의해 생성된 sum은 호출시 this가 o 객체를 향하고 있습니다.

```js
function sum() {
  return this.a + this.b + this.c;
}
var o = {
  a: 1,
  b: 2,
  c: 3,
  get average() {
    return (this.a + this.b + this.c) / 3;
  }
};
Object.defineProperty(o, 'sum', {
    get: sum, enumerable: true, configurable: true});
console.log(o.average, o.sum); // 2, 6
```