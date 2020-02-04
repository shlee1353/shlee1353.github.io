---
title: React 리덕스(Redux) Immutable.js 상태관리 & Ducks 파일 구조
date: 2020-02-04 10:35:04
categories:
- React
tags:
---

![](/image/react-immutable-redux/1.png)

자바스크립트에서 불변성 데이터를 다룰 수 있도록 도와주는 것이 바로 Immutable.js입니다. 우선, 객체 불변성에 대해 살펴 보도록 하겠습니다.

```js
let a = 5;
let b = 5;

let object1 = { a: 1, b: 3};
let object2 = { a: 1, b: 3};
```
a, b값은 같으며, === 연산자를 사용하면 true값이 나옵니다. 원시값에서는 이 등식이 성립하지만, 객체에서는 다릅니다. 값이 같더라도 서로 다른 객체이기 때문에 false값이 나옵니다.

```js
let array1 = [1, 2, 3, 4, 5];
let array2 = array1;

array2.push(6);
array1 === array2
```

배열에 값을 추가해서 비교를해보면 어떨까요? 이때도 true값이 나옵니다. 리액트 컴포넌트는 state 또는 상위 props값이 변할 때 마다 리렌더링이 됩니다. 이때 얕은 비교를 하며, 올바른 비교를 하기 위해서 또는 성능 최적화를 위해서 이전과 업데이트 되는 데이터의 비교에서 상태유지는 필수입니다.

실제로 불변성을 유지하기 위해서는 코드가 복잡해지는데요, 이때 Immutable.js 라이브러리를 사용하면 좀 더 편리하게 코드 작성이 가능합니다. Immutable에서는 객체 대신 Map 데이터 구조를 사용합니다.

```js
const { Map, fromJS } = Immutable;

const data = fromJS({
  a: 1,
  b: 2,
  c: {
    d: 3,
    e: 4,
    f: 5
  }
});

data.get('a');
data.getIn(['c', 'd']);

const newData = data.set('a', 10);
const newData = data.merge({ a: 10, b: 10});
const newData = data.setIn(['c', 'd'], 20)
                    .setIn(['c', 'e'], 20);
```

값을 업데이트하거나 불러올 때는 내장된 함수를 사용하면 됩니다. 다음으로 List에 대해서 알아 보도록 하겠습니다. 배열과 동일하게 map, filter, sort, push, pop 함수를 내장하고 있습니다.

```js
const { Map, List, fromJS } = Immutable;

const list = List([0, 1, 2, 3, 4]);
const list2 = fromJS([
  { value: 1 },
  { value: 2 }
])

list.get(0);
list2.getIn([0, 'value']);
```

아이템 수정은 set, setIn을 사용하며 update를 사용하는 방법도 있습니다.

```js
const list = List([
  Map({value: 1}),
  Map({value: 2})
])

const newList = list.set(0, Map({value: 10}));
newList.getIn([0, 'value']);

const newList2 = list.update(0, item => item.set('value', item.get('value') * 10));
newList2.getIn([0, 'value']);
```

아이템 추가는 push를 사용합니다. 새 리스트를 만들어서 리턴하기 때문에 기존 자바스크립트 push함수와 다릅니다. 아이템 제거는 delete를 사용합니다.

```js
Map({value: 1}),
  Map({value: 2})
])

const newList = list.push(Map({value: 3}));
const newList = list.unshift(Map({value: 0}));
newList.getIn([2, 'value']);

const newList = list.delete(0);
const newList = list.pop();
```

List의 크기와 비어 있는지 확인 하려면 size, isEmpty를 사용하면 됩니다.

```js
const list = List([
  Map({value: 1}),
  Map({value: 2})
])

console.log(list.size)
console.log(list.isEmpty())
```

지금까지 Immutable.js에 대해 간단히 알아봤습니다. 지금부터는 Ducks 파일 구조에 대해 간략 하게 알아보도록 하겠습니다. 리덕스는 기본적으로 액션 타입, 액션 생성함수, 리듀서 세 종류로 이루어져 있습니다. 이 세 가지 파일을 한 파일에서 모듈화하여 관리하는 것을 말합니다.

```js
// 액션 타입
const CREATE = 'app/todos/CREATE';

// 액션 생성 함수
export const create = (todo) => ({
  type: CREATE,
  todo,
})

const initialState = {
  
}

// 리듀서
export default function reducer(state = initialState, action) {
  switch(action.type) {
      
  }
}
```

Ducks 구조에서는 지켜야할 규칙이 있습니다.

- export default 를 사용하여 리듀서를 내보야 합니다.
- export를 사용하여 액션 생성 함수를 내보야 합니다.

redux-actions 패키지의 createAction과 handleActions을 함께 이용하면 더 쉬운 액션 관리가 가능합니다.

```js
export const increment = (index) => ({
  type: types.INCREMENT,
  index
});

export const increment = createAction(types.INCREMENT);
increment(3);
increment({index: 1, color: '#fff'});
```
createAction 함수는 액션 생성 함수를 간단하게 만들어줍니다. 리듀서에서 switch 문을 사용하여 액션 type에 따라 작업한 부분도 handleActions를 사용하여 scope문제도 해결할 수 있습니다.

```js
const reducer = handleActions({
  INCREMENT: (state, action) => ({
    counter: state.counter + action.playload
  })
}, {counter: 0});
```