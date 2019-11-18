---
title: 리액트(React) 라이프사이클 v16.3 간단정리
date: 2019-07-04 19:11:50
categories:
- React
tags:
---

![](/image/react-component-lifecycle/1.png)

# 리액트 라이프 사이클

리액트버전 16.3에서 새로운 라이프 사이클 2개가 소개되었습니다. getDerivedStateFromProps, getSnapshotBeforeUpdate 이며 기존에 사용하던componentWillMount, componentWillUpdate, componentWillReceiveProps 는 점차 지원이 중단되어 버전 17에서는 완전히 삭제될 예정이라고 합니다.

컴포넌트 라이프사이클 초기 화면 로딩시:

- constructor
- getDerivedStateFromProps
- render
- componentDidMount

첫 화면 랜더링 후 props가 바뀔경우:

- getDerivedStateFromProps
- shouldComponentUpdate (if return false일 경우 종료, true면 랜더진행)
- render
- getSnapshotBeforeUpdate
- componentDidUpdate

state가 바뀔 경우에는 바로 shouldComponentUpdate 부터 라이프사이클이 진행 됩니다. 라이프 사이클의 기본적인 사용방법과 각각의 라이프 사이클에 대해 알아 보도록 하겠습니다.

## 1. Initializing state

```jsx
class ExampleComponent extends React.Component {
  state = {
    currentColor: this.props.defaultColor,
    palette: 'rgb',
  };
}
```

state값 초기화 시, constructor 또는 state 객체에 바로 추가하면 됩니다.

## 2. Fetching external data

```jsx
componentDidMount() {
    this._asyncRequest = asyncLoadData().then(
      externalData => {
        this._asyncRequest = null;
        this.setState({externalData});
      }
    );
  }
```

외부 데이터를 추가 시 componentDidMount에 추가합니다.

## 3. Adding event listeners

```jsx
componentDidMount() {
    // Event listeners are only safe to add after mount,
    // So they won't leak if mount is interrupted or errors.
    this.props.dataSource.subscribe(
      this.handleSubscriptionChange
    );

    // External values could change between render and mount,
    // In some cases it may be important to handle this case.
    if (
      this.state.subscribedValue !==
      this.props.dataSource.value
    ) {
      this.setState({
        subscribedValue: this.props.dataSource.value,
      });
    }
  }
```

이벤트 리스너 또한 componentDidMount에 추가합니다.

## 4. Updating state based on props

```jsx
static getDerivedStateFromProps(props, state) {
    if (props.currentRow !== state.lastRow) {
      return {
        isScrollingDown: props.currentRow > state.lastRow,
        lastRow: props.currentRow,
      };
    }
```

state의 변화가 필요하면, state 객체를 반환합니다. 이는 마치, this.setState({stateName: value})와 같습니다. state 변화가 필요없을 경우는 null을 반환합니다.

## 5. Invoking external callbacks

```jsx
componentDidUpdate(prevProps, prevState) {
    if (
      this.state.someStatefulValue !==
      prevState.someStatefulValue
    ) {
      this.props.onChange(this.state.someStatefulValue);
    }
  }
```

**constructor()**

이 메소드는 새로운 객체가 생성될 때 마다 호출됩니다. super(props)은 부모 클래스의 생성자를 호출하며 this.props 로 접근이 가능합니다. 또한, 콜백으로 전달될 함수들을 바인딩 할 때도 사용됩니다. this.setState() 또는 Ajax 요청을 사용하시면 안됩니다. (componentDidMount()에서 사용)

**static getDerivedStateFromProps()**

이 메소드는 컴포넌트 초기화 또는 새로운 props를 받았을 때 일어납니다. 인스턴스가 아닌 클래스로 존재합니다. 그렇기 때문에 this 키워드 접근이 불가능합니다. this.setState() 사용 대신 state를 업데이트 하기 위한 객체를 리턴하며 업데이트가 필요없을 경우 null값을 리턴합니다. 오로지 변화가 생긴 state만 리턴하고 나머지는 유지됩니다. this.setState()는 이 메소드를 호출하지 못합니다.

**houldComponentUpdate()**

새로운 렌더링 과정이 필요 없을 경우 라이프사이클을 빠져나올 수 있습니다. 리엑트는 기본적으로 깊은 비교를 하지 않습니다. 그렇기 때문에 props, state가 업데이트 될 경우 리렌더링이 필요하다고 생각합니다.

**render()**

이 메소드가 호출되면 this.props와 this.state가 분석되며 리액트 엘리먼트를 리턴합니다. render() 메소드는 state를 변경하면 안되며 브라우저와 직접적으로 상호작용하면 안됩니다. 브라우저와 연동하려면 componentDidMount()안에서 하면 됩니다. shouldComponentUpdate()가 false를 리턴할 경우 발생하지 않습니다.

**componentDidMount()**

이 메소드는 전체 라이프사이클에서 한 번만 호출됩니다. 데이터와 관련된 로직을 실행하기에 적합합니다. componentDidupdate()는 prevProps, prevState, snapshot을 파라미터로 가질 수 있습니다. 이 외에도 componentWillUnmount(), componentDidCatch()가 있습니다.