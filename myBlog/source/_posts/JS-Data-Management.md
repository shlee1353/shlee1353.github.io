---
title: '[자바스크립트] 데이터 관리 object, array'
date: 2017-11-15 14:08:41
thumbnail: https://shlee1353.github.io/img/javascript.png
tags:
---

### JSON
JSON is a syntax for storing and exchanging data. JSON is text, and we can convert any JavaScript object into JSON, and send JSON to the server. If you receive data in JSON format, you can convert it into a JavaScript object:
```js
var myObj = { "name":"John", "age":31, "city":"New York" };
var myJSON = JSON.stringify(myObj);

var myJSON = '{ "name":"John", "age":31, "city":"New York" }';
var myObj = JSON.parse(myJSON);
```

### For..in loop
```js
var charactersBooks = {
    Frodo: "Lord of the Rings",
    Aslan: "Chronicles of Narnia",
};

var characters = [ ];
var title = [ ];

// get key
for (var key in charactersBooks) {
    if (charactersBooks.hasOwnProperty(key)) {
        characters.push(key);
    }
}

// get value
for (var key in charactersBooks) {
    if (charactersBooks.hasOwnProperty(key)) {
        title.push(charactersBooks[key]);
    }
}

// result
console.log(characters); // ["Frodo", "Aslan"]
console.log(title); // ["Lord of the Rings", "Chronicles of Narnia"]

```
charactersBooks 이라는 객체가 있습니다. 이 객체는 주인공과 책 제목을 프로퍼티로 가지고 있습니다. 이제 characters와 title 두 배열에 주인공과 책 제목을 추가 하도록 하겠습니다. 객체의 key와  value값을 가져오는 방법은 매우 다양합니다. 저희가 사용할 방법은 **for..in** 반복문 입니다. 반복문과 함께 **hasOwnProperty**도 사용하도록 하겠습니다. 이 메소드는 객체가 특정 프로퍼티를 자기만의 직접적인 프로퍼티로서 소유하고 있는지를 판단하는데 사용됩니다.  **in** 연산과는 다르게, 이 메소드는 객체의 프로토타입 체인을 확인하지 않습니다. ([in과 hasOwnProperty 사용법](http://adripofjavascript.com/blog/drips/the-uses-of-in-vs-hasownproperty.html))

### Merge two arrays
```js
var characters = ["Frodo", "Aslan"];
var title =  ["Lord of the Rings", "Chronicles of Narnia"];

Array.prototype.push.apply(characters, title);
console.log(characters); 
// ["Frodo", "Aslan", "Lord of the Rings", "Chronicles of Narnia"]

var characterList = characters.concat(title);
console.log(characterList); 
// ["Frodo", "Aslan", "Lord of the Rings", "Chronicles of Narnia"]
```
두 배열을 하나의 배열로 만드는 방법 중 하나는 기존에 있던 배열에 새로운 배열을 추가하는 방법이 있습니다. 다른 하나는 두 배열을 합쳐 새로운 배열을 만드는 방법이 있습니다. 기존 배열에 추가를 할 때는Array.prototype.push.apply(thisArg, [argsArray])를 사용합니다. apply() 메소드는 주어진 this값과 arguments로 함수를 호출합니다. arguments에는 배열 또는 유사배열객체가 올 수 있으며 함수를 호출할때 입력된 이자가 담겨져 있습니다. 함수에 전달될 여러 개의 인자를 받는 call()구문과 유사합니다. [arguments](http://devbox.tistory.com/entry/JavaScript-arguments%EC%9C%A0%EC%82%AC-%EB%B0%B0%EC%97%B4-%EA%B0%9D%EC%B2%B4)가 유사배열이기 때문에 push()를 사용할 수가 없어서 Array.prototype에 있는 push()를 불러와야 합니다.

만약, 배열 안에 있는 문자열을 합치려면 +=, join(), concat()을 사용하시면 됩니다. [브라우저 속도 차이](https://www.codeschool.com/discuss/t/string-concatenation-join-v-v-concat/5307)가 있기 때문에 필요에 맞게 사용하시면 됩니다. 일반적으로는 += 를 사용하는 것이 성능상 좋다고 합니다.

https://www.zerocho.com/category/Javascript/post/57433645a48729787807c3fd

### Converting a JS object to an array

```js
var charactersBooks = {
    Frodo: "Lord of the Rings",
    Aslan: "Chronicles of Narnia",
};

var arrayCharacters = Object.keys(charactersBooks).map(function(key){
    return {[key]: charactersBooks[key]};
})

console.log(arrayCharacters); // [Object, Object]

var length = Object.keys(arrayCharacters).length;

console.log(length);
```
지금까지 객체의 key값과 value값을 가져와 각각의 빈 배열에 추가하는 방법을 알아봤습니다. 이제는 객체 전체를 배열로 한번에 전환하는 방법을 알아 보도록 하겠습니다. Object.keys().map()을 사용하시면 됩니다. Object.keys() 메소드는 객체의 열거할 수 있는 속성값이 담긴 배열을 리턴합니다. 리턴된 배열은 map()을 통해 조건에 부합하는 새로운 배열을 생성합니다. 이렇게 생선된 배열은 arrayCharacters로 정의된 변수에 저장됩니다. 아래는 map()을 사용해서 객체에 담긴 firstname과 lastname을 가져와 join()을 사용해서 fullname으로 출력하는 방법입니다.
```js
var persons = [
    {firstname : "Malcom", lastname: "Reynolds"},
    {firstname : "Kaylee", lastname: "Frye"},
    {firstname : "Jayne", lastname: "Cobb"}
];

function getFullName(item, index) {
    // join() method joins the elements of an array into a string, and returns the string.
    var fullname = [item.firstname,item.lastname].join(" ");
    return fullname;
}

function myFunction() {
    document.getElementById("demo").innerHTML = persons.map(getFullName);
}
```

### Creating an array of objects
```js
var journal = [];

function addEntry(events, didITurnIntoASquirrel) {
	journal.push({
		events: events,
		squirrel: didITurnIntoASquirrel
	});
}

addEntry(["work", "touched tree", "pizza", "running", "television"], false);
addEntry(["work", "touched tree", "pizza", "running", "television"], false);
```

### 객체복사하기
자바스크립트에서 객체는 일반 문자열, 숫자, 불린과 다르게 복사가 됩니다. 값을 복사하는게 아니라 참조하기 때문입니다. 즉, 하나의 객체를 여러개의 변수가 바라보고 있기 때문에 객체를 수정할 경우 모든 변수에 영향을 줍니다. 이것을 얕은복사라고 합니다. 두 개의 객체를 메모리에 할당하는 것을 깊은복사라고 합니다.

```
// extend 얕은복사
var copiedObject = jQuery.extend({}, originalObject)
// extend 깊은복사
var copiedObject = jQuery.extend(true, {}, originalObject)
```

```js
var array = ['a', 'b', 'c'];
var deep = Array.prototype.slice.call(array);
deep[0] = 'd';
console.log(array); // ['a', 'b', 'c']
```



참고
- http://blog.kazikai.net/?p=16
- https://www.zerocho.com/category/Javascript/post/5750d384b73ae5152792188d
- https://hyunseob.github.io/2016/02/08/copy-object-in-javascript/
