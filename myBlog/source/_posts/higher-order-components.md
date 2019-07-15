---
title: 리액트(React) 순수함수 (HOC 개념정리)
date: 2019-07-04 19:45:02
tags:
---

![](/image/higher-order-components/1.png)

Higher-Order Components HOC는 컴포넌트 로직을 재사용하기 위한 리액트의 고급기술입니다. 함수로서 컴포넌트를 인자로 받아 새로운 컴포넌트를 리턴합니다.

```jsx
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

기존 컴포넌트가 props를 받아 UI를 만들었다면, HOC는 컴포넌트를 다른 컴포넌트로 바꿉니다. HOC는 Redux’s connect, Realy’s createFragmentContainer에서 흔히 사용하고 있습니다. 리액트에서 컴포넌트는 코드 재사용을 위한 매우 중요한 유닛입니다.

https://reactjs.org/docs/higher-order-components.html

ComponentList와 BlogPost는 동일하지 않지만, 같은 로직을 가지고 있습니다.

```jsx
addChangeListener() -> handleChange() -> removeChangeListener()
```

앱 전체적으로 봤을 때 이러한 로직은 자주 사용되는 패턴입니다. 이러한 로직을 하나의 장소에서 관리하고 여러 컴포넌트에서 사용할 수 있도록 추상화작업이 필요합니다. 이럴때 HOC가 필요합니다. DataSource를 구독하고 있는 CommentList, BlogPost 컴포넌트를 제작할 수 있는 함수를 만들어 보도록 하겠습니다. 이 함수는 인자로 데이터를 prop로 받을 수 있는 자식 컴포넌트를 받습니다. 이 함수를 withSubscription 이라고 부르도록 하겠습니다.


**첫번째 파라미터는 wrapped 컴포넌트이며, 두 번째 파라미터로 DataSource와 현재 props값을 받습니다.**

CommentListWithSubscription과 BlogPostWithSubscription이 랜더되면, CommentList와 BlogPost는 data prop로 전달됩니다. 이 값에는 DataSource에서 받은 현재값도 포함됩니다. HOC는 주입된 컴포넌트를 수정하지도 않으며 inheritance를 사용하지도 않습니다. wrapped 컴포넌트는 새로운 props, data는 물론 부모 container의 모든 props를 받습니다. Wrapped component는 어떻게 왜 데이터가 사용되는지, 어디서 오는지 고민할 필요가 없습니다.

리액트의 HOC의 구조를 보면 고차함수가 생각납니다. 고차함수를 만족시키는 조건으로는, 첫째, 함수를 파라미터로 전달 받는 함수, 둘째, 함수를 리턴하는 함수입니다.

## 어디에 쓰이나?

1. 비즈니스 로직을 담당하는 컴포넌트와 디스플레이를 담당하는 컴포넌트를 분리하여 사용할때
2. 로딩 중 화면표시, 유저 인증 로직 처리 (컴포넌트 내에서 권한 체크나 로그인 상태를 체크인 하기 보다는 인증로직을 HOC로 분리)

아래와 같이 Button, Label 컴포넌트가 있습니다.

```jsx
import React from 'react';
import { render } from 'react-dom';
class App extends React.Component {
  render(){
    return(
      <div>
        <Button>button</Button>
        <hr/>
        <Label>label</Label>
      </div>
    )
  }
}
const Button = (props) => <button>{props.children}</button>
class Label extends React.Component {
  render() {
    return(
      <label>{this.props.children}</label>
    )
  }
}
render(<App />, document.getElementById('root'));
```

HOC 컴포넌트는 컴포넌트를 인자로 받아서 새로운 컴포넌트를 리턴합니다.

```jsx
const HOC = (InnerComponent) => class extends React.Component {
  render(){
    return(
      <InnerComponent />
    )
  }
}
```

**완성코드**

```jsx
import React from 'react';
import { render } from 'react-dom';
const HOC = (InnerComponent) => class extends React.Component {
  constructor() {
    super();
    this.state = {count: 0}
  }
  update(){
    this.setState({count: this.state.count + 1})
  }
  render(){
    return(
      <InnerComponent 
        {...this.props}
        {...this.state}
        update={this.update.bind(this)}
      />
    )
  }
}
class App extends React.Component {
  render(){
    return(
      <div>
        <Button>button</Button>
        <hr/>
        <LabelHOC>label</LabelHOC>
      </div>
    )
  }
}
const Button = HOC((props) => 
  <button onClick={props.update}>{props.children} - {props.count}</button>
)
class Label extends React.Component {
  render() {
    return(
      <label onMouseMove={this.props.update}>
      {this.props.children} - {this.props.count}
      </label>
    )
  }
}
const LabelHOC = HOC(Label)
render(<App />, document.getElementById('root'));
```

실무에서 storybook 컴포넌트를 정리하면서 HOC를 사용했습니다. 예를들어, true, false에 따라 열리고 닫히는 모달팝업이 여러개 있을 경우, 열고/닫기 로직을 HOC로 만들어 놓고 이를 여러개의 모달 컴포넌트에 적용하였습니다. 특별히, storybook 내부에서는 state관리가 직관적으로 되지않기 때문에 이를 해결하는 방법으로 HOC사용 및 플러그인이 있습니다.