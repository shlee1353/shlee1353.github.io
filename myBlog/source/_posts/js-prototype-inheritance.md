---
title: 자바스크립트(JavaScript) 상속과 프로토타입 개념정리
date: 2019-07-05 10:46:04
categories:
- JavaScript
tags:
---

![](/image/js-prototype-inheritance/1.png)

자바스크립트에는 클래스라는 개념대신 기존의 객체를 복사하여 새로운 객체를 생성하는 프로토타입 언어입니다. 프로토타입은 크게 두 가지로 나뉘어 집니다. 프로토타입 객체를 참조하는 prototype 속성(Prototype Object) 과 객체 멤버인 proto 속성이 참조하는 숨은 링크(Prototype Link) 입니다. 다시말해, proto (숨은링크)는 상위에서 물려받은 객체의 프로토타입에 대한 정보이며 prototype 프로퍼티는 자신을 원형으로 만들어질 새로운 객체들 즉 하위로 물려줄 연결에 대한 속성입니다.

자바스크립트에서 함수가 정의될 때 2가지가 일어납니다. 1. 해당함수에 constructor(생성자) 자격부여. 2. 해당 함수의 prototype object 생성 및 연결. 함수를 생성하면 함수 뿐만 아니라 생성된 함수와 연결되는 Prototype Object도 함께 생겨납니다. 이 객체에 접근하려면 prototype 이라는 속성을 통해 가능합니다. 기본속성으로 constructor와 proto 를 가지고 있습니다. proto 속성은 함수뿐만 아니라 모든 객체가 가지고 있습니다. 객체 생성시 조상이었던 함수의 Prototype Object를 가리킵니다.

## Prototype Object

함수를 정의하면 다른 곳에 생성되는 프로토타입 객체는 자신이 다른 객체의 원형이 되는 객체입니다. 모든 객체는 프로토타입 객체에 접근할 수 있습니다. Prototype Object 는 자기 자신의 분신이며 자신을 원형으로 만들어질 다른 객체가 참조할 프로토타입이 됩니다.

```js
function Person(){}
var joon = new Person();  
var jisoo = new Person();
Person.prototype.getType = function (){  
    return "인간"; 
};
console.log(joon.getType());   // 인간  
console.log(jisoo.getType());  // 인간
```

Prototype Link 자바스크립트에서 기본 데이터 타입을 제외한 모든 것이 객체입니다. 객체가 만들어지기 위해서는 자신을 만드는 데 사용된 원형인 프로토타입 객체를 이용하여 객체를 만듭니다. 이때 만들어진 객체 안에 proto (비표준) 속성이 자신을 만들어낸 원형을 의미하는 프로토타입 객체를 참조하는 숨겨진 링크가 있습니다. 이 숨겨진 링크를 프로토타입이라고 정의합니다.

```js
function A() {};
var A = new A();
console.log(A);
```

콘솔을 확인해 보면, proto 와 constructor 를 주의 깊게 봐야합니다. 해당 객체의 프로토타입은 A라는 함수 객체이며 이 객체의 생성자 역시 function A() 함수이다. 즉, new 를 사용해 만들어진 객체는 function A()를 자신의 프로토타입으로 사용해서 만들어졌습니다.

## 상속(Inheritance)

```js
function Person() {
 this.name = "annonymous";
 this.sayHello = function() {
  alert("Hello, my name is" + this.name);
 }
}
function Yoda() {
 var obj = new Person();
 obj.name = "Yoda";
 return obj;
}
var me = new Yoda();
me.sayHello();
console.log(me instanceof Yoda); // false
console.log(me instanceof Person); // true
```

초창기 자바스크립트 상속 개념은 2개의 객체가 있을 경우, 하나의 객체에서 다른 객체의 함수 사용을 목적으로한 제한적 상속방법 이였습니다. 예제 코드와 같이 new를 사용해서 obj를 만들고 리턴하는 방법입니다. 하지만, 내부를 살펴보면 me의 인스턴스는 Yoda가 아닌 Person으로 나옵니다.

```js
var person = {
 name: "annoymous",
 sayHello: function() {
  alert("Hello, my name is" + this.name);
 }
};
function Yoda() {
 this.name = "Yoda";
}
Yoda.prototype = person;
var yoda = new Yoda();
yoda.sayHello();
person.sayHello();
console.log(yoda instanceof Yoda); // true
```

이러한 문제 때문에 function에서 기본적으로 제공하는 prototype을 이용한 방법이 등장 하였습니다. person을 리터럴 방식으로 생성한 뒤 객체형태로 프로퍼티와 메소드를 입력합니다. 이렇게 하면 yoda는 Yoda의 인스턴스로 true값을 반환합니다. 하지만, person이 함수형태가 아니기 때문에, instanceof Person를 이용한 부모객체 확인이 불가능합니다. 자바스크립트에서 함수가 중요한 이유는 프로토타입 속성을 제공하는 유일한 객체이기 때문입니다. 이해를 돕기위해, 앞으로 계속 등장하는 prototype과 constructor에 대해 간단히 알아 보도록 하겠습니다. http://www.nextree.co.kr/p7323/

```js
function Car () {};
Car.prototype; // Object
Car.prototype = {
  constructor : function() {},
  __proto__ : Object
};
var audi = new Car();
audi = {
    __proto__ : {
        constructor : function f () {},
        __proto__ : Object
    }
};
```

Car라는 함수(객체)를 생성한 뒤, 내부를 살펴보면, constructor와_proto_가 있습니다. constructor는 car 함수가 생성될 때 사용된 함수객체를 나타내며, _proto_는 현재 프로토타입에 연결된 부모 객체입니다. 여기에 new를 사용하면, 함수의 prototype을 근간으로하는 신규 객체가 반환됩니다. 자바스크립트의 생성자함수에 대해 좀 더 알아보도록 하겠습니다. 객체(Object)는 Object Literal Notation을 사용해서 생성할 수도 있을 뿐 아니라, Constructor 라는 함수를 이용해서도 생성이 가능합니다. 일반적으로 new Object() 또는 Object Literal Notation을 이용해 생성한 객체들은 모두 자바스크립트 내장 함수인 Object() 라는 객체 생성자(Constructor)함수를 이용해 생성되는 것입니다.


```js
var person1 = {
  firstName: "Jason",
  lastName: "Bourne",
  age: 25  
};
var person2 = new Object();
console.log(person1.constructor); //function Object() 
console.log(person2.constructor); //function Object() 
console.log(person1.constructor == person2.constructor); //true
var array = [];
var number = new Number(3);
var bool = new Boolean(true);
var str = new String("String");
console.log(array.constructor); //function Array()
console.log(number.constructor); //function Number()
console.log(bool.constructor); //function Boolean()
console.log(str.constructor); //function String()
function Person(firstName, lastName, age) {
  this.firstName = firstName;
  this.lastName = lastName;
  this.age = age;
  this.getInfo = function() {
    return "Name : " + firstName + " " + lastName + "\nAge : " + age;
  }
}
var person1 = new Person("Jason", "Bourne", 33);
var person2 = new Person("Jenny", "Laurence", 18);
console.log(person1.constructor); //function Person
console.log(person2.constructor); //function Person
```


```js
function Person() {
 this.name = "annoymous";
 this.sayHello = function() {
  alert("Hello, my name is" + this.name);
 }
} 
function Yoda() {
 this.name = "Yoda";
}
Yoda.prototype = new Person();
var yoda = new Yoda();
yoda.sayHello();
console.log(yoda instanceof Yoda); // true
console.log(yoda instanceof Person); // true
```

다시 상속으로 돌아와서, prototype을 new로 생성하여 Yoda.prototype으로 지정하는 방법이 있습니다. 이럴경우, yoda는 Yoda와 Person의 인스턴스로 확인이 가능합니다. 하지만, 여기에도 문제가 있습니다. 실제 생성자는 Yoda임에도 불구하고 내부 constructor은 일치하지 않습니다. 그럼에도 불구하고, instanceof를 확인해보면 둘다 true가 나오는데 이러한 이유는 instanceof는 prototype을 기반으로 작동하기 때문입니다.

```js
Object.create = function(o) {
 function F() {}
 F.prototype = o;
 return new F();
}
```

Constructor를 제대로 연결하기 위해 Object.create()가 표준으로 등장하였으며, IE9 이상부터 사용이 가능합니다. 이 함수는 prototype을 인자로 받아 기본함수 F의 prototype()으로 설정을 한 뒤 new 명령어를 통해 새로운 객체를 리턴합니다.

```js
function Person() {
 this.name = "annoymous";
 this.sayHello = function() {
  alert("Hello, my name is" + this.name);
 }
} 
function Yoda() {
    Person.call(this);
 this.name = "Yoda";
}
Yoda.prototype = Object.create(Person.prototype);
Yoda.prototype.constructor = Yoda;
var yoda = new Yoda();
yoda.sayHello();
```

우선 Object.create()를 사용하여 Person.prototype을 상속받습니다. 다음으로, constructor를 Yoda 로 재선언 해준 뒤, call()를 사용하여 조상 메소드에 접근이 가능합니다. 결국, 자바스크립트는 상속은 생성자와 프로토타입에 기반을 두고 있습니다. ES6 에서는 클래스를 사용하여 이를 간단히 표현할 수 있습니다.

```js
class Person {
 constructor (name, age) {
  this.name = name;
  this.age = age
 }
 printProfile() {
  console.log(this.name + this.age);
 }
}
```

ES6 문법으로 작성한 위 내용은 결국 아래와 같습니다.

```js
function Person(name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.printProfile = function() {
 console.log(this.name + this.age);
}
```

ES6에서 클래스 상속 구현은 extends 절과 super 키워드를 사용하여 구현합니다. extends 절은 클래스가 다른 생성자의 정적/비정적 프로퍼티를 상속 가능하게 합니다. super 키워드는 클래스 constructor 메소드에서 부모 생성자를 호출하거나, 클래스 메소드 내부에서 부모 생성자의 정적/비정적 메소드를 참조할 때 사용합니다.

```js
class Vehicle {
constructor (name, type) {
    this.name = name;
    this.type = type;
  }
  getName () {
    return this.name;
  }
  getType () {
    return this.type;
  }
}
class Car extends Vehicle {
  constructor (name) {
    super(name, 'car');
  }
  getName () {
    return 'It is a car: ' + super.getName();
  }
}
let car = new Car('Tesla');
console.log(car.getName()); // It is a car: Tesla
console.log(car.getType()); // car
```

상속의 예시를 살펴보면, Vehicle 의 프로퍼티값으로 name과 type이 있으며, getName(), getType() 두 메소드를 가지고 있습니다. Car는 extends와 super()를 사용하여 Vehicle를 상속받아 car라는 인스턴스를 생성하였습니다. 이 과정에서 getName()은 Car 내부에서 다시 정의가 되었습니다. 마지막에 생성된 car는 Vehicle과 Car의 메소드에 접근할 수 있습니다. 자바스크립트 상속과정을 설명하면서 this가 많이 나왔는데요, 이것도 간단히 정리해보도록 하겠습니다.

```js
function sayHello () { 
    var greeting = 'hello'; 
} 
alert(greeting); // undefined
```

자바스크립트에서 함수는 위와같이 유호범위(scope)를 생성합니다. 현재 코드에서는, 외부에서 접근이 불가능합니다. 컨텍스트(context)는 내부에서 코드가 돌아가는 객체를 의미합니다. 이때, this를 사용해서 컨텍스트를 참조할 수 있습니다.

```js
// function 내부 컨텍스트
function f () {
    console.log(this); 
};
f(); // window(전역객체)를 가리킴
// 메소드 내부 컨텍스트
var oj = {
    method : function () {
      console.log(this);
    }
};
oj.method(); // 메소드를 담은 객체를 가리킴
// 메소드 내부에 중첩된 함수의 컨텍스트
var oj = {
    method : function () {
        function inner () {
            console.log(this);  // window(전역객체)를 가리킴
        }
};
```