---
title: Redux Core Concepts
date: 2017-11-14 15:35:47
tags:
---

### Redux?
**Redux** is a state management library that lets you connect directly to application state from anywhere in your app. It also allows you manipulate application state from anywhere in your app. But, to work its magic, Redux requires that your app have a single data store.

리덕스는 자바스크립트 앱의 상태를 포함하고 있는 컨테이너입니다. Redux store라고 부릅니다. 애플리케이션의 모든 상태를 불변의 객체 트리에 저장합니다. 리덕스는 createStore(reducer, [initialState], [enhancer]) 명령어를 통해 생성할 수 있습니다. 사실, 리덕스는 React뿐만 아니라, Angular, Ember, Vue.js 에도 사용이 가능합니다. Redux를 React와 사용할 때 가장 쉬운 방법은 React Redux 바인딩 라이브러리를 사용하는 것입니다. 이 라이브러리를 사용하면 리덕스 상태와 액션을 props와 쉽게 연결 할 수 있습니다.

### Why do we need Redux?
You can't pass a property directly to the component or components that you wish to target. The reason has to do with how React works. React enforces a chain of command where properties have to flow down from a parent component to an immediate child component. ([Link](https://www.kirupa.com/react/transferring_properties.htm))
```
var Display = React.createClass({
  render: function() {
    return(
      <div>
        <p>{this.props.color}</p>
        <p>{this.props.num}</p>
        <p>{this.props.size}</p>
      </div>
    );
  }
});
 
var Label = React.createClass({
  render: function() {
    return (
      <Display color={this.props.color}
              num={this.props.num}
              size={this.props.size}/>
    );
  }
});
 
var Shirt = React.createClass({
  render: function() {
      return (
        <div>
          <Label color={this.props.color}
                 num={this.props.num}
                 size={this.props.size}/>
        </div>
      );
    }
});
 
ReactDOM.render(
  <div>
    <Shirt color="steelblue" num="3.14" size="medium"/>
  </div>,
  document.querySelector("#container")
);
```
**Spread Operator**
```
var items = ["1", "2", "3"];
 
function printStuff(a, b, c) {
  console.log("Printing: " + a + " " + b + " " + c);
}
 
// using the spread operator
printStuff(...items);
 
// without using the spread operator
printStuff(items[0], items[1], items[2]);
```

```
var Display = React.createClass({
  render: function() {
    return(
      <div>
        <p>{this.props.color}</p>
        <p>{this.props.num}</p>
        <p>{this.props.size}</p>
      </div>
    );
  }
});
 
var Label = React.createClass({
  render: function() {
    return (
      <Display {...this.props}/>
    );
  }
});
 
var Shirt = React.createClass({
  render: function() {
      return (
        <div>
          <Label {...this.props}/>
        </div>
      );
    }
});
 
ReactDOM.render(
  <div>
    <Shirt color="steelblue" num="3.14" size="medium"/>
  </div>,
  document.querySelector("#container")
);
```
It's not good practice to have all components connect to application state. It is best to have parent/container components connect to state and pass state directly to children.

## Redux의 4 가지 메서드
- store.getState() - 현재 상태 객체 트리를 리턴.
- store.dispatch(action) - 상태를 바뀌기 위히 액션을 보냄.
- store.subscribe(listener) - 상태 트리에서 변화를 감지.
- store.replaceReducer(nextReducer) - 현재 리듀서를 다른 것으로 교체 (advanced).

### Redux 3가지 원칙
- 애플리케이션의 state를 위해 단 한개의 store를 사용합니다.
- 애플리케이션에서 store의 state를 직접 변경할 수 없습니다. state를 변경하기 위해선 무조건 action 이 dispatch 되어야 합니다.
- action 객체를 처리하는 순수함수를 reducer 라고 부릅니다. reducer는 정보(현재 상태 트리와 액션)를 받아서 어떻게 업데이트 할 지 정의합니다. reducer는 순수함수로 작성되어야 합니다. 즉, 네트워크 및 데이터베이스 접근 불가, 인수 변경 불가. 순수하지않은 API 사용불가 Date.now() Math.random()

### 카툰 안내서
http://bestalign.github.io/2015/10/26/cartoon-intro-to-redux/
- **액션 생성자(action creators)**: 애플리케이션의 상태를 바꾸고 싶다면 항상 액션을 보내야만 하며 유일한 방법입니다.
- **스토어(store)**: 상태트리(state tree) 전체를 유지하는 책임을 지며, 액션이 들어왔을 때 어떤 상태변화가 필요한지에 대한일은 reducer에게 위임한다.
- **리듀셔(the reducers)**: 스토어는 액션이 어떤 상태 변화를 만드는지 알 필요가 있을 때 리듀서에게 묻는다. 리듀서는 넘겨받은 예전 상태를 변경하지 않고 새로운 복사본을 만든 후 모든 변경사항을 적용한다. 리듀서는 복사되고 업데이트된 상태 객체를 루트 리듀서에게 넘겨주고, 루트 리듀서는 이 객체를 스토어로 보낸다.
- **똑똑한 컴포넌트**: 액션 처리를 책임진다. 자기 자신의 CSS style을 가지지 않는다. 자신의 DOM을 거의 가지고 있지 않는다.
- **뷰 레이어 바인딩(the view layer binding)**: 스토어를 뷰에 연결하기 위한 도움장치.

### 동작방법
1. 스토어를 준비: 루트 컴포넌트는 createStore()를 이용해서 스토어를 생성하고 무슨 리듀서를 사용할지 알려준다. 필요한 모든 리듀서를 combineReducers()를 이용해서 하나로 묶는다.
2. 루트 컴포넌트는 공급 컴포넌트로 서브 컴포넌트를 감싸고 스토어와 공급 컴포넌트 사이를 연결한다. 똑똑한 컴포넌트는 connect()로 네트워크에 연결한다. 이를 통해 상태 업데이트를 받을 수 있게 만든다.
3. 액션콜백 준비: 멍청한 컴포넌트가 액션과 쉽게 일할 수 있게 bindActionCreators()로 액션 콜백을 준비.

### 데이터흐름

1. 뷰가 액션을 요청한다. ([Image](http://makeitopen.com/static/images/redux_flowchart.png))
2. bindActionCreators()가 준비과정에서 사용되었으면 자동으로 액션이 보내진다.
3. 스토어가 액션을 받는다. 현재 애플리케이션 상태 트리와 액션을 루트 리듀서에게 보낸다.
4. 루트 리듀서는 상태 트리를 조각으로 나눈 뒤 알맞은 서브 리듀서로 상태 조각들을 넘겨준다.
5. 서브 리듀서는 받은 상태 조각을 복사한 뒤, 그 복사본을 변경한다. 루트 리듀서에게 변경된 복사본을 돌려준다.
6. 모든 변경된 조각들을 돌려주면, 루트 리듀서는 조각들을 하나로 모아 상태 트리로 만든 뒤 스토어로 돌려준다. 스토어는 새로운 상태 트리를 옛날 상태 트리와 바꾼다.
7. 스토어는 뷰 레이어 바인딩에게 애플리케이션 상태가 변경되었다는 것을 알린다.
8. 뷰 레이어 바인딩은 스토어에게 새로운 상태를 보내달라고 요청한다.
9. 뷰 레이어 바인딩은 뷰에게 화면을 업데이트하도록 요청한다.

### 프로젝트 준비
https://www.youtube.com/watch?v=rNhstoL9MME&list=PL9FpF_z-xR_GMujql3S_XGV2SpdfDBkeC&index=23

폴더구조
```
src
    ㄴactions
        ㄴActionTypes.js
        ㄴindex.js
    ㄴcomponents
        ㄴApp.js
        ㄴControl.js
        ㄴCounter.js
        ㄴValue.js
    ㄴreducers
        ㄴcounter.js
        ㄴindex.js
        ㄴui.js
    ㄴindex.js
```

### actions
리덕스로 보내질 객체를 리턴합니다. 리턴되는 객체는 dispatch 될 때 인자로 전달됩니다.
```
//ActionTypes.js
export const INCREMENT = "INCREMENT";
export const DECREMENT = "DECREMENT";
export const SET_COLOR = "SET_COLOR";
```

```
//index.js
import * as types from './ActionTypes';

export function increment() {
    return {
        type: types.INCREMENT
    };
}

export function decrement() {
    return {
        type: types.DECREMENT
    };
}

export function setColor(color) {
    return {
        type: types.SET_COLOR,
        color
    };
}
```
### components
```
//App.js
import React, { Component } from 'react';
import Counter from './Counter';

class App extends Component {

    render() {
        return(
            <Counter/>
        );
    }
}


export default App;
```
```
//Control.js
import React, { Component, PropTypes } from 'react';

const propTypes = {
    onPlus: PropTypes.func,
    onSubtract: PropTypes.func,
    onRandomizeColor: PropTypes.func
};

function createWarning(funcName) {
    return () => console.warn(funcName + ' is not defined');
}
const defaultProps = {
    onPlus: createWarning('onPlus'),
    onSubtract: createWarning('onSubtract'),
    onRandomizeColor: createWarning('onRandomizeColor')
};

class Control extends Component {

    render() {
        return(
            <div>
                <button onClick={this.props.onPlus}>+</button>
                <button onClick={this.props.onSubtract}>-</button>
                <button onClick={this.props.onRandomizeColor}>Randomize Color</button>
            </div>
        );
    }
}

Control.propTypes = propTypes;
Control.defaultProps = defaultProps;

export default Control;
```
```
//Counter.js
import React, { Component } from 'react';

import Value from './Value';
import Control from './Control';
import { connect } from 'react-redux';
// import { connect, bindActionCreators } from 'redux';

import * as actions from '../actions';


class Counter extends Component {

    constructor(props) {
        super(props);
        this.setRandomColor = this.setRandomColor.bind(this);
    }

    setRandomColor() {
        const color = [
            Math.floor((Math.random()*55) + 200),
            Math.floor((Math.random()*55) + 200),
            Math.floor((Math.random()*55) + 200)
        ];

        this.props.handleSetColor(color);
    }

    render() {

        const color = this.props.color;
        const style = {
            background: `rgb(${color[0]}, ${color[1]}, ${color[2]})`
        };

        return(
            <div style={style}>
                <Value number={this.props.number}/>
                <Control
                    onPlus={this.props.handleIncrement}
                    onSubtract={this.props.handleDecrement}
                    onRandomizeColor={this.setRandomColor}
                />
            </div>
        );
    }
}

const mapStateToProps = (state) => {
    return {
        number: state.counter.number,
        color: state.ui.color
    };
};

const mapDispatchToProps = (dispatch) => {
    //return bindActionCreators(actions, dispatch);
    return {
        handleIncrement: () => { dispatch(actions.increment())},
        handleDecrement: () => { dispatch(actions.decrement())},
        handleSetColor: (color) => { dispatch(actions.setColor(color))}
    };
};

// 두 개의 인수를 받으며 리덕스 스토어와 컴포넌트를 연결하는 함수를 반환합니다.
export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```
```
//Value.js
import React, { Component, PropTypes } from 'react';

const propTypes = {
    number: PropTypes.number
};

const defaultProps = {
    number: -1
};

class Value extends Component {

    render() {
        return(
            <div>
                <h1>{this.props.number}</h1>
            </div>
        );
    }
}

Value.propTypes = propTypes;
Value.defaultProps = defaultProps;

export default Value;
```
### reducers
```
//counter.js
import * as types from '../actions/ActionTypes';
// 애플리케이션의 초기 상태를 나타냅니다.
const initialState = {
    number: 0,
    dummy: 'dumbdumb',
    dumbObject: {
        d: 0,
        u: 1,
        m: 2,
        b: 3
    }
};

export default function counter(state = initialState, action) {
    /* ... */
    switch(action.type) {
        case types.INCREMENT:
            return {
                ...state,
                number: state.number + 1,
                dumbObject: { ...state.dumbObject, u: 0 }
            };
        case types.DECREMENT: 
            return {
                ...state,
                number: state.number - 1
            };
        default:
            return state;
    }
}
```
```
//index.js
import { combineReducers } from 'redux';
import counter from './counter';
import ui from './ui';


const reducers = combineReducers({
    counter, ui
});

export default reducers;
```
```
//ui.js
import * as types from '../actions/ActionTypes';

const initialState = {
    color: [255, 255, 255]
};

export default function ui(state = initialState, action) {
    if(action.type === types.SET_COLOR) {
        return {
            color: action.color
        };
    } else {
        return state;
    }
}
```
### index.js
```
import React from 'react';
import ReactDOM from 'react-dom';

import App from './components/App';

import { createStore } from 'redux';
import reducers from './reducers';

//Provider 컴포넌트는 React store를 모든 자식 컴포넌트에 제공
import { Provider } from 'react-redux';

//스토어를 생성하여 상태변화를 감지할 수 있으며 액션을 보낼 수 있음
const store = createStore(reducers);

console.log(store.getState());
store.subscribe(() => console.log(store.getState()));
store.dispatch(actions.increment());
store.dispatch(actions.decrement());
store.dispatch(actions.setColor([200, 200, 200]));


ReactDOM.render(
    <Provider store={store}>
        <App/>
    </Provider>,
    document.getElementById('root')
);
```

sources
- https://github.com/shlee1353/phostalgia/tree/demo (advanced example)
- http://lorenstewart.me/2016/11/27/a-practical-guide-to-redux/
- https://www.kirupa.com/react/transferring_properties.htm

