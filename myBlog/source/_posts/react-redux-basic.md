---
title: 리액트 리덕스(Redux)기본 개념 및 사용방법
date: 2020-01-30 11:26:19
categories:
- React
tags:
---

![](/image/react-redux-basic/1.png)

액션은 스토어에서 상태 변화를 일으킬 때 참조하는 객체입니다. 이 객체는 type 값을 반드시 가지고 있어야 합니다. 아래와 같은 액션은 상태값에 존재하는 수치를 증가 시키는데 사용되며 이 값을 어떻게 업데이트 할지는 리듀서 함수에서 정의합니다.

```js
{
 type: "INCREMENT"
}
```

액션을 좀 더 편하게 사용하기 위해서 액션 생성함수(action creator)를 사용합니다. 이 함수는 액션 타입을 상수 값으로 정해 주면 됩니다.

```js
const INCREMENT = 'INCREMENT';

const increment = (diff) => ({
  type: INCREMENT,
  diff: diff
})
```

태에 변화를 일으키려면 리듀서 함수가 필요합니다. 두개의 파라미터를 받으며 첫 번째는 현재 상태이며, 두 번째는 액션 객체입니다.

```js
const initialState = {
 number: 1,
 foo: 'bar'
}

function counter(state = initialState, action) {
 switch(action.type) {
  case INCREMENT:
  return Object.assign({}, state, {
   number: state.number + action.diff
  })
 }
}
```

상태값을 업데이트 할때 `Object.assign(target, …sources)`를 사용하면 기존의 값을 보존한 체 원하는 값만 업데이트 할 수 있습니다. 전개 연산자를 사용하면, 더욱더 깔끔하게 작성이 가능합니다.

```js
return {
   ...state,
   number: state.number + action.diff
}
```

액션과 리듀서가 준비되면 리덕스 스토어 생성을 합니다. 스토어를 생성할 때는 createStore 함수를 사용하며 파라미터로 리듀서 함수가 들어갑니다.

```js
const { createStore } = Redux;
const store = createStore(counter);

const unsubscribe = store.subscribe(() => {
  console.log(store.getState())
})

store.dispatch(increment(3))
store.dispatch(increment(2))
```

스토어를 구독할 때는 subscribe를 사용하며 미리 만들어 놓은 액션들은 dispatch를 통해 스토어에 추가할 수 있습니다. 실무에서는 react-redux 라이브러리에 내장된 Provider를 통해서 손쉽게 스토어로 연결이 가능합니다.

```js
import { createStore } from 'redux';
import reducers from './reducers';
import { Provider } from 'react-redux';

const store = createStore(reducers);

ReactDOM.render(
 <Provider store={store}>
  <App/ >
 </Provider>,document.getElementById('root')
)
```

그 다음으로 react-redux 라이브러리의 connect 함수를 사용하여 컴포넌트와 연결을 합니다. 각각의 파리미터의 용도를 살펴보도록 하겠습니다.

```js
connect([mapStateToProps], [mapDispatchToProps], [mergeProps])
```

- mapStateToProps: store.getState() 결과 값인 state를 파라미터로 받아 컴포넌트의 props로 사용할 객체를 반환합니다.
- mapDispatchToProps: dispatch를 파라미터로 받아 액션을 디스패치하는 함수들을 객체 안에 넣어서 반환합니다.

```js
const mapStateToProps = (state, ownProps) => {
 color: state.color,
 number: state.number,
 todo: state.todos[ownProps.id]
}

const mapDispatchToProps = (dispatch) => {
 onIncrement: action => dispatch(increment(action))
}
```