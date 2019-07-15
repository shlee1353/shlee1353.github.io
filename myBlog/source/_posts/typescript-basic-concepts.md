---
title: 타입스크립트(TypeScript) 기본개념 및 사용방법 정리
date: 2019-07-05 14:32:20
tags:
---

![](/image/typescript-basic-concepts/1.png)

```ts
let v1 = 123;
v1 = 'abc';
```

타입스크립트에서 위와같이 선언시 두 번째 v1은 타입 에러가 발생합니다. 이는 첫 번째 할당 시 문자열이 아닌 숫자가 변수에 담겨서 입니다. 이것을 타입추론(type inference)이라고 합니다. 두 가지 모두 변수에 담기 위해서는 두 가지 타입을 명시해줘야 합니다.

```ts
let v1: number | string = 123;
v1 = 'abc';
```

이처럼 타입스크립트에서는 여러 가지 타입을 정의할 수 있습니다. any 타입은 모든 종류의 값을 허용합니다.

```ts
const values: number[] = [1, 2, 3];
const values: Array<number> = [1, 2, 3];

const data: [string, number] = [msg, size];

let value: any;
value = 123;
value = '123';
value = () => {};
```

**열거형 타입**

```ts
enum Fruit {
	Apple,
	Banana,
	Orange,
}

const v1: Fruit = Fruit.Apple;
const v2: Fruit.Apple | Fruit.Banana = Fruit.Banana;
```

열거형 타입은 객체이며, 각 원소는 값으로 사용될 수 있고, 타입으로 사용될 수도 있습니다. 상수 열겨형 타입을 사용할 경우 컴파일 후에도 남아 있지 않습니다.

**함수 타입**

```ts
function getInfoText(name: string, age: number): string {

}
```

함수 타입을 정의하기 위해서는 매개변수 타입과 반환 타입이 필요합니다.

**인터페이스**

자바에서의 인터페이스는 클래스를 구현하기 전에 필요한 메서드를 정의하는 용도로 사용됩니다. 타입스크립트에서는 좀 더 다양한 기능을 가지고 있습니다. 인터페이스로 객체 타입을 정의하는 것을 알아보도록 하겠습니다.

```ts
interface Person {
	name: string;
	age: number;
}
```

Person 인터페이스에서 하나 이상의 속성 타입을 만족하지 못하면 타입 에러가 발생합니다. 선택속성을 추가하려면 ? 기호를 사용해야합니다. 읽기 전용일 경우 readonly를 추가하면 됩니다. 정의되지 않은 속성값을 할당할 경우 타입 에러가 발생합니다.

```ts
interface Person {
    readonly name: string;
    age?: number;
}

const p1: Person = {
    name: 'mike',
    birthday: '222-22-22',
}
```

**제네릭**

```ts
function makeArray<T>(defaultValue: T, size: number): T[] {
    const arr: T[] = [];
    for(let i = 0; i < size; i++) {
        arr.push(defaultValue);
    }
    return arr;
}

const arr1 = makeArray<number>(1, 10);
const arr2 = makeArray<string>('abc', 10);
const arr3 = makeArray('abc', 10);
```

제네릭은 타입 정보가 동적으로 결정되며, 같은 규칙을 여러 타입에 적용할 수 있습니다.

**함수형 컴포넌트의 타입 정의하기**

```jsx
import React from 'react';

interface Props {
	name: string;
	age?: number;
}

const MyComponent: React.FunctionComponent<Props> = function({ name, age = 23 }) {
	return (
		<>
			<p>{name}</p>
			<p>{age.substr(0)}</p>
		</>
	);
};
```

**클래스형 컴포넌트의 타입 정의하기**
```jsx
import React, { createRef } from 'react';

interface Props {
	containerStyle: React.CSSProperties;
	theme: string;
}

const defaultProps = {
	theme: 'dark',
}

interface State {
	name: string;
	age: number | undefined;
}

class MyComponent extends React.Component<Props, State> {
	state = {
		name: 'mike',
		age: undefined,
	};
	static defaultProps = defaultProps;
	pRef = createRef<HTMLParagraphElement>();
	onClick1 = (e: EventObject) => {
		console.log(e.currentTarget.dataset['food']);
	};
	onClick2 = (e: React.MouseEvent<HTMLButtonElement>) => {
		console.log(`${e.clientX}, ${e.clientY}`);
	};
	render() {
		const { containerStyle, theme } = this.props;
		const { name, age } = this.state;

		return (
			<div style={containerStyle}>
				<p ref={this.pRef}>{name}</p>
				<button data-foold="soup" onClick={this.onClick1}>버튼1</button>
				<button onClick={this.onClick2}>버튼2</button>
			</div>
		)
	}
}
```

**참고링크**
- https://overreacted.io/ko/how-are-function-components-different-from-classes/