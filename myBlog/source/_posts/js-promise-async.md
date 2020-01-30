---
title: 자바스크립트 비동기 프로그래밍 (promise, async, await) 정리
date: 2019-11-08 15:45:40
categories:
- JavaScript
tags:
---

![](/image/js-promise-async/0.png)

자바스크립트는 웹에서 사용되는 언어이며 사용자의 요청에 맞게 DB에 저장되어 있는 데이터를 보여줘야합니다. 자바스크립트 언어는 단일 스레드 언어(위에서 아래로 단방향으로 함수가 해석 되며 실행)이기 때문에, 행여 필요한 데이터가 클라이언트 쪽으로 전달되지 않은 채로 사용자에게 보여진다면 서비스상 큰 오류가 생길 수 있습니다. 그렇기 때문에 이러한 문제점을 해결하기 위해 비동기 프로그래밍이 도입되었습니다.

### 비동기란?

자바스크립트에서 정의한 코드가 완료되지 않아도 다음 코드를 실행하는 것을 말합니다. 예로, 제이쿼리의 ajax를 생각하시면 됩니다. 자바스크립트 내부 메소드인 setTimeout 메소드 또한 비동기 방식으로 작동합니다.

```js
var f1 = function(arg, callback) {
    $.ajax({
        success: function(data) {
            callback(data);
        }
    });
}
// 데이터를 성공적으로 받으면 파라미터로 넘겨준 콜백함수에 값이 넘어가 실행됨
f1(arg, function(data) { 
           var a = f2(data);
           alert(a);
        }
 );
```

비동기의 단점 중 하나는 바로 흔히들 말하는 콜백지옥입니다. 콜백함수를 남용해서 생긴 결과로 가독성이 떨어지며 코드 유지보수를 어렵게 만듭니다. 이러한 콜백지옥을 해결하기 위해 등장한 것이 ES6의 프로미스(Promise)입니다.

### Promise

```
프로미스는 하나의 객체로 미래의 특정 값을 생성하며 3가지의 상태값을 가지고 있습니다.
```

프로미스의 상태를 이해하고 있어야 사용이 가능합니다.

- Pending(대기) : 비동기 처리 로직이 아직 완료되지 않은 상태
- Fullfilled(이행) : 비동기 처리가 완료되어 프로미스가 결과값을 반환해준 상태
- Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태

```js
function create() {
 return new Promise(function(resolve, reject){
  resolve();
  console.log("step1");
 });
};

create().then(function(){
 console.log("step3-succeed");
}, function(){
 console.log("step3-failed");
});
console.log("step2");
```

Promise의 처리 순서를 알아보도록 하겠습니다. 우선, `function create()`가 생성되며 실행되지않고 다음 단계로 넘어갑니다. `create().then(function(){…})`에서 crate()함수가 실행됩니다. create()가 호출되면서 내부에 선언되었던 코드가 실행됩니다. new Promise()의 인스턴스가 리턴되며 콜백함수로 선언해 놓은 function(){}이 실행됩니다. resolve()는 선언되었지만 조건이 충족되기 전까지 실행되지 않으며 step1이 실행됩니다. 바로, return을 통해 생성된 인스턴스를 반환합니다. resolve()는 나중에 호출할 수 있는 환경이 되면 실행됩니다. create() 실행이 완료되면 이어서 then()을 호출 할 수 있지만 바로 아래 코드인 step2가 실행됩니다. Promise 비동기의 핵심은

1. resolve()는 호출할 수 있는 환경이되면 호출된다.
2. then()을 실행하지 않고 아래 코드로 이동한다.

step2 실행이 완료되면 이제 then()내부에 작성된 function()이 실행될 차례입니다. 함수가 두 개가 있는데 첫 번째는 성공시, 두 번째는 실패시 실행됩니다. 내부에 작성된 함수는 true/false값에 따라 둘 중 하나가 실행됩니다. 지금까지 Promise의 기본 메커니즘에 대해 간단히 알아봤습니다. Promise를 사용하면 기존 콜백함수 보다 깔끔하게 코드작성이 가능하지만, 실행 절차/순서가 복잡하여 가독성이 떨어집니다. 그래서 ES8에서 async/await가 등장 하였습니다.

### async&await

사용방법이 간단하고 직관적이기 때문에 활용도가 높습니다. 우선 함수 앞에 async를 붙여주고 비동기로 처리되는 곳에 await를 추가합니다. 여기서 주의할 점은 await 뒷부분은 반드시 promise를 반환해야하며, async함수 자체도 promise를 반환합니다.
async/await 간단한 활용 예시를 살펴보도록 하겠습니다. 아래 코드를 직접 실행하면 비동기처리의 결과값이 콘솔에 찍혀 나오는 것을 확인 할 수 있습니다.

```js
function doubleAfter2Seconds(x) {
  console.log('value:' + x)
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x * 2);
    }, 2000);
  });
}

async function addAsync(x) {
  const a = await doubleAfter2Seconds(10);
  const b = await doubleAfter2Seconds(20);
  const c = await doubleAfter2Seconds(30);
  return x + a + b + c;
}

addAsync(10).then((sum) => {
  console.log('result:' + sum);
});
출처: https://codeburst.io/
```

async 함수가 호출되면 Promise를 리턴합니다. async함수에서 값을 리턴하면, promise는 그 값을 받아서 resolved됩니다. async함수는 await 표현식을 포함하고 있으며 async 함수에 Promise 값이 전달되기 전까지 실행을 지연시킵니다. 지금까지 비동기프로그래밍 promise, async, await에 대해 간단히 알아봤습니다.

### 리액트에서 Async Await 사용하기

리액트에서 `async/await` 사용은 방법만 알고 있으면 크게 어려운것은 없습니다.

1. `async` 키워드를 함수 앞에 추가한다.
2. `await`를 함수의 body 안에 사용한다.
3. 오류 처리를 한다.

```jsx
import React, { Component } from "react";
import ReactDOM from "react-dom";

class App extends Component {
  constructor() {
    super();
    this.state = { data: [] };
  }
  async componentDidMount() {
    const response = await fetch(`https://api.coinmarketcap.com/v1/ticker/?limit=10`);
    const json = await response.json();
    this.setState({ data: json });
  }

  render() {
    return (
      <div>
        <ul>
          {this.state.data.map(el => (
            <li>
              {el.name}: {el.price_usd}
            </li>
          ))}
        </ul>
      </div>
    );
  }
}

export default App;
ReactDOM.render(<App />, document.getElementById("app"));
```

