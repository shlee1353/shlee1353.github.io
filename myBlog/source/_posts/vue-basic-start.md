---
title: 뷰(Vue) 기본개념 간단히 알아보기
date: 2019-10-14 13:58:25
tags:
---

![](/image/vue-basic-start/1.png)

## 설치
```
sudo npm install -g vue-cli
vue init simple hellovuejs // 애플리케이션 생성
```
- 애플리케이션을 생성하고 index.html 파일을  아래처럼 수정하도록 하겠습니다.
- 리액트나 앵귤러와 달리 초기 환경설정이 매우 간편하다는 장점이 있습니다.

```js
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>02-01</title>
    <script src="https://unpkg.com/vue/dist/vue.min.js"></script>
</head>
<body>
    <div id="simple">
        <h2>{{message}}</h2>
    </div>
    <script type="text/javascript">
        var model = {
            message : "첫 번째 Vue.js 앱 입니다!"
        };

        var simple = new Vue({
            el : '#simple',
            data : model
        })
    </script>
</body>
</html>
```

- Vue.js 는 기본적으로 MVVM 디자인 패턴을 사용하고 있습니다.

```js
// View
<div id="simple">
    <h2>{{message}}</h2>
</div>
```
- View는 화면상에 보여지는 영역이며 중괄호 두개를 사용하여 데이터 값과 연동됩니다.

```js
// View Model
var simple = new Vue({
	el : '#simple',
	data : model
})

// Model
var model = {
	message : "첫 번째 Vue.js 앱 입니다!"
};
```
- 모델(Model)을 변경하면 뷰모델(ViewModel) 객체를 통해 HTML DOM이 즉시 변경됩니다.
- index.html 파일을 실행 한 뒤 콘솔 창을 띄웁니다. 그 다음 아래 코드를 넣어주시면, 화면상에서 바로 값이 반영되는 것을 확인 할 수 있습니다.

```js
model.message = "hello Vue.js";
```

## 기본 디렉티브
- 미리 선언해 놓은 디렉티브를 사용하면 좀 더 효율적으로 Vue.js를 사용하실 수 있습니다.

**v-text**

- v-text는 이전에 사용한 중괄호 두개와 같은 기능을 하며 자바스크립트의 innerText 속성과 연결됩니다. (사용권장)

```js
<div id="simple">
    <h2 v-text="message"></h2>
</div>
```
**v-html [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-02.html)**

- v-html은 innerHTML 속성과 연결되며 태그 문자열을 파싱하여 화면에 나타냅니다.

```js
<div id="simple">
    <h2 v-html="message"></h2>
</div>
```

**v-bind [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-03.html)**

- v-bind는 요소 객체의 속성들을 단방향 바인딩 하기위해 사용합니다.

```js
<input id="a" type="text" v-bind:value="message">
<img v-bind:src="imagePath" />

var model = {
	message : 'v-bind 디렉티브',
	imagePath : 'http://sample.bmaster.kro.kr/photos/61.jpg'
};

```

**v-model [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-04.html), [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-05.html)**

- 앞에서 살펴본 단방향 바인딩은 HTML 요소에서 값을 변경하더라도 모델 객체의 값이 바뀌지 않습니다. 
- v-model은 요소에서 변경한 값이 모델 객체에 반영되는 양방향 바인딩을 제공합니다.

```js
<input type="text" v-model="name" placeholder="이름을 입력하세요" />
입력된 이름 : <h2 v-html="name"></h2>

var twoway = new Vue({
    el : '#simple',
    data : {
        name : ''
}
```

- v-model 디렉티브는 텍스트 박스뿐만 아니라 다양한 입력 폼 필드에서도 사용 가능합니다.
- 링크 되어 있는 예시코드를 살펴보면 배열 fruits을 가지고 있는 model을 두 개의 Vue 객체(#simple1, #simple2)에서 참조합니다.

```js
<input type="checkbox" value="1" v-model="fruits">사과, 
<input type="checkbox" value="2" v-model="fruits">키위, 
<input type="checkbox" value="3" v-model="fruits">포도,
<input type="checkbox" value="4" v-model="fruits">수박,
<input type="checkbox" value="5" v-model="fruits">참외

선택한 과일들 : <span v-html="fruits"></span>

var model = {
	fruits : []
}
```

**v-show [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-06.html)**,
**v-if [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-07.html)**

- v-show, v-if는 Vue 객체의 data 속성 값에 따라 렌더링 여부를 결정하는 기능입니다.
- 둘의 차이점은 v-show는 일단 HTML 요소를 렌더링한 후에 display 속성으로 화면단을 제어합니다.
- v-if 디렉티브는 조건에 부합되지 않으면 렌더링을 하지 않습니다.
- 자주 화면이 변경되는 부분에 대해서는 v-show 디렉티브를 사용하는 것이 바람직합니다.

```js
// v-show
<input type="text" v-model="amount" />
<img v-show="amount < 0" />

// v-if
<input type="text" v-model="balance" />
<span v-if="balance >= 1000000">Gold</span>
<span v-else-if="balance >= 500000">Silver</span>
```

**v-for [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-08.html), [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-09.html)**

- 반복적인 데이터 렌더링을 하기 위해 v-for 디렉티브를 사용합니다.
- 예시코드를 확인하면 model에서 contacts 배열을 가져와 화면에 보여주고 있습니다.

```js
<tr v-for="(contact, index) in contacts">
    <td>{{index+1}}</td>
    <td>{{contact.name}}</td>
    <td>{{contact.tel}}</td>
    <td>{{contact.address}}</td>
</tr>
```

- 객체인 경우는 키를 이용해 값에 접근합니다.

```js
<option v-for="(val, key, index) in regions" v-bind:value="key">{{ index+1 }} : {{val}}</option>
```

## 기타 디렉티브 및 계산형 속성
**v-pre [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-11.html)**,
**v-once [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-12.html)**

- v-pre 디렉티브는 HTML 요소에 대한 컴파일을 수행하지 않습니다.
- v-once 디렉티브는 HTML 요소를 단 한 번만 렌더링하기 때문에 데이터를 변경하더라도 다시 렌더링되지 않습니다.


**계산형 [예시](https://github.com/stepanowon/vuejs_book/blob/master/ch02/02-13.html)**

- 연산 로직이 필요할 경우 계산형 속성(Computed Property)를 사용해하여 필요한 함수를 등록할 수 있습니다.
- 함수 안에서 this는 Vue 객체 자신을 참조합니다.
- num 값이 문자열로 다루어지기 때문에 Number(), parseInt() 함수를 사용해서 명시적으로 숫자로 변환해주어야 합니다.

```js
computed : {
    sum : function() {
        var n = Number(this.num);
        if (Number.isNaN(n) || n < 1)  return 0;
        return ((1+n) * n) / 2;
    }
}
```
- Vue.js도 리액트 처럼 렌더링 속도 향상을 위해 가상DOM을 사용합니다. 가상 DOM에 대한 간단한 [영상](https://www.youtube.com/watch?v=BYbgopx44vo)

## Vue 인스턴스
**el, data, computed**

Vue 인스턴스에는 el, data, computed 옵션이 있습니다. 먼저 data 옵션에 주어진 모든 속성들은 Vue 인스턴스 내부에서 직접 이용되지 않고 Vue 인스턴스와 Data 옵션에 주어진 객체 사이에 프록시를 두어 처리합니다. 그래서 값에 접근할 때 vm.name으로 가능합니다. 직접 data에 접근하려면 vm.$data.name 방법이 있습니다.

```js
var model = {
	name : "Ryan"
}
var vm = new Vue({
	el : '#test',
	data : model
})
```
el 옵션은 Vue 인스턴스에 연결할 HTML DOM요소를 지정하며 여러개 요소에 지정할 수 없습니다. Computed 옵션에 지정한 것은 함수였지만 Vue 인스턴스는 프록시 처리하여 마치 속성처럼 취급합니다.

**메서드**

Vue 인스턴스에서 사용할 메서드를 등록하는 옵션입니다. 직접호출, 디렉티브 포현식, 콧수염 표현식에서도 사용할 수 있습니다. 계산형 속성과 차이가 있다면 결과값의 캐싱 여부입니다. 메서드는 캐싱없이 매번 실행합니다.

```js
<span>{{sum()}}</span>
.
.
methods :  {
	sum : function() {
		var n = Number(this.num);
		if (Number.isNaN(n) || n < 1) return 0;
		return ((1+n)*n)/2;
	}
}
```

**관찰속성**

관찰속성은 긴 처리 시간이 필요한 비동기 처리에 적합하다는 특징을 가지고 있습니다.

## 컴포넌트 심화
**전역 컴포넌트**

```js
// HTML
<div id="example">
  <my-component></my-component>
</div>
.
.
// JS
// 등록
Vue.component('my-component', {
  template: '<div>사용자 정의 컴포넌트 입니다!</div>'
})

// 루트 인스턴스 생성
new Vue({
  el: '#example'
})
.
.
// 랜더링 결과
<div id="example">
  <div>사용자 정의 컴포넌트 입니다!</div>
</div>
```
**단일 컴포넌트와 전역 컴포넌트의 차이점**

- &#60;template&#62; 에는 id 특서을 부여하지 않음
- &#60;script&#62; 영역에서는 Vue 컴포넌트의 template을 지정하지 않음
- Vue.component()로 이름과 template 속서을 지정하지 않음
- 단일 컴포넌트는 name 속성을 지정해 반드시 객체를 export 해야함
- 컴포넌트에서 사용할 스타일은 &#60;style&#62; 내부에 작성

**전역 수준 컴포넌트의 문제점**

- 빌드 단계가 없으므로 최신 자바스크립트 문법을 사용할 수 없습니다.
- CSS 스타일을 빌드하고 모듈화할 수 있는 기능을 제공하지 않습니다.
- 컴포넌트 템플릿 작성시 HTML 파일안에 여러개의 &#60;template &#47;&#62; 태그가 작성되어야 하기 때문에 식별이 어렵습니다.

Vue-CLI가 제공하는 프로젝트 템플릿 중 webpack-simple에 포함되어 있는 vue-loader라는 구성 요소가  단일 파일 컴포넌트를 지원합니다. 확장자가 .vue인 파일에  &#60;template &#47;&#62;,  &#60;script &#47;&#62;,  &#60;style &#47;&#62; 을 작성하면 vue-loader는 이 파일을 파싱하고 다른 로더들을 활용해 하나의 모듈로 조합합니다. 특히, css-loader를 이용해 CSS 스타일을 전처리할 수 있으며, 스타일 정보를 모듈화할 수도 있습니다.

App.vue 파일을 열어보면 &#60;template &#47;&#62;,  &#60;script &#47;&#62;,  &#60;style &#47;&#62; 3개의 기본 영역을 확인할 수 있습니다. App.vue 컴포넌트를 화면에 담기 위해 main.js를 사용합니다.

```js
import Vue from 'vue'
import TodoList from './components/TodoList.vue'

new Vue({
  el: '#app',
  render: h => h(TodoList)
})
```
App.vue를 비롯한 .js 파일들은 트랜스파일되고, 번들링되어 ./dist/build.js 파일을 생성하여 최종적으로 index.html 파일에 보여집니다.

```js
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="utf-8">
    <title>todolistapp</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="/dist/build.js"></script>
  </body>
</html>
```

본격적으로, 예제로 살펴볼 todolistapp 디렉토리와 파일구조는 다음과 같습니다.

```
src
	ㄴcomponents
 		ㄴEventBus.vue
 		ㄴInputTodo.vue
 		ㄴList.vue
 		ㄴTodoList.vue
	ㄴmain.js
index.html
```

1.EventBus.vue

```js
<script type="text/javascript">
	import Vue from 'vue';
	var eventBus = new Vue(); // 이벤트버스 생성
	export default eventBus;
</script>
```
2.InputTodo.vue

```js
<style>...</style>

<template>
	<div>
	    <input class="input" type="text" id="task" v-model.trim="todo" 
	        placeholder="입력 후 엔터!" v-on:keyup.enter="addTodo">
	    <span class="addbutton" v-on:click="addTodo">추 가</span>
	</div>    
</template>

<script type="text/javascript">
	import eventBus from './EventBus.vue' // Import 이벤트 버스
	export default {
	    name : 'input-todo',
	    data : function() {
	        return { todo : "" }
	    },
	    methods : {
	        addTodo : function() {
	            eventBus.$emit('add-todo', this.todo); // 이벤트 발행
	            this.todo = "";
	        }
	    }
	}
</script>
```
- input을 통하여 자동으로 trim 된 값이(v-model.trim="todo") data로 전달됩니다.
- 엔터 또는 버튼을 누르면 addTodo가 실행되고, this.todo의 값을 포함한 'add-todo' 이벤트를 등록합니다.

3.List.vue

```js
<style>...</style>

<template>
    <ul id="todolist">
        <li v-for="(a, index) in todolist" v-bind:class="checked(a.done)"
            v-on:click="doneToggle(index)">
            <span>{{ a.todo }}</span>
            <span v-if="a.done"> (완료)</span>
            <span class="close" v-on:click.stop="deleteTodo(index)">&#x00D7;</span>
        </li>
    </ul>
</template>

<script type="text/javascript">
	import eventBus from './EventBus.vue'
	export default {
	    created : function() {
	         eventBus.$on('add-todo', this.addTodo); // 이벤트 구독
	    },
	    data : function() {
	        return {
	            todolist : [
	                { todo : "영화보기", done:false },
	                { todo : "주말 산책", done:true },
	                { todo : "ES6 학습", done:false },
	                { todo : "잠실 야구장", done:false },
	            ]
	        }
	    },
	    methods : {
	        checked : function(done) {
	            if(done) return { checked:true };
	            else return { checked:false };
	        },
	        addTodo : function(todo) {
	            if (todo !== "") {
	                this.todolist.push({ todo : todo, done:false });
	            }
	        },
	        doneToggle : function(index) {
	            this.todolist[index].done = !this.todolist[index].done;
	        },
	        deleteTodo : function(index) {
	            this.todolist.splice(index,1);
	        }
	    }
	}
</script>

```
- eventBus.$on을 통하여 'add-todo' 이벤트를 받아 this.addTodo를 실행합니다.
- 전달된 todo값의 유무를 확인 후 true이면 todolist 배열에 넘어온 값을 추가 합니다.
- EventBus.$off()를 사용하면 이벤트를 제거할 수 있습니다.

4.TodoList.vue

```js
<style>...</style>
<template>
    <div id="todolistapp">
        <div id="header" class="header">
            <h2>Todo List App</h2>
            <input-todo />
        </div>
        <list></list>
    </div>  
</template>
<script type="text/javascript">
	import InputTodo from './InputTodo.vue';
	import List from './List.vue';
	export default {
	    name : 'todo-list',
	    components : { InputTodo, List }
	}
</script>
```
**스타일 적용**

기존에는 스타일 태그를 이용한 전역 스타일을 적용하였습니다. 만약, 다른 컴포넌트에서도 동일한 CSS 클래스명을 사용한다면 충돌이 발생합니다. 특정 컴포넌트의 스타일을 지정하려면 범위CSS와 모듈CSS 두 가지 방법이 있습니다.

1.범위CSS

```js
src
	ㄴcomponents
 		ㄴChild1.vue
 		ㄴChild11.vue
 		ㄴChild2.vue
	ㄴmain.js
index.html
```

Child1.vue, Child2.vue에는 모두 main 클래스의 스타일이 적용되어 있습니다. 이럴경우, 두 스타일이 충돌되면서 마지막에 선언된 스타일이 적용됩니다. 이때, 스타일 태그에 scoped를 추가하면 이를 해결할 수 있습니다. 범위 CSS를 적용하면 data-v-xxxxx 형태의 속성이 부여됩니다.

```js
<style scoped>
.main{border:1px solid #000;background-color:aqua}
</style>
```
주의점

- 특성 선택자(attribute selector)를 사용하기 때문에 브라우저에서 스타일 적용 속도가 느립니다. 그렇기 대문에 속도가 빠른 ID, 클래스, 태그명 선택자로 요소를 선택해 스타일을 적용해야 합니다.
- 부모 컴포넌트에 적용된 범위CSS는 하위 컴포넌트에도 반영이 됩니다.

2.CSS모듈

CSS모듈은 CSS를 객체처럼 다루는 것을 의미합니다. 설정하는 방법은 간단합니다. 스타일 태그에 module을 추가하면 CSS 모듈 모드가 작동되면서 그 결과로 생성된 클래스 식별 객체는 $style이름으로 컴포넌트의 계산형 속성으로 추가됩니다. 이 스타일은 Vue 인스턴스 내에서 $style 이라는 계산형 속성을 통해서 이용할 수 있습니다.

```js
<button :class="$style.hand"> CSS Module을 적용한 버튼</button>
.
.
<style module>
.hand{cursor:pointer;background-color:purple;color:yellow}
</style>
```

적용해야 할 클래스가 여러개일 경우 배열 문법을 이용하며, 자바스크립트에서 접근이 가능합니다.

```js
<div v-bind:class"[$style.box, $style.border]">Hello World</div>
.
.
export default {
	created() {
		console.log(this.$style);
	}
}
```
**슬롯**

- 기본사용법

슬롯을 이용해 부모 컴포넌트에서 자식 컴포넌트로 HTML 마크업을 전달 할 수 있습니다. 슬롯을 사용하기 위해서 자식 컴포넌트에서는 &#60;slot&#62;&#60;&#47;slot&#62; 태그를 작성하고 부모 컴포넌트에서는 콘텐츠 영역에서 자식 컴포넌트의 &#60;slot&#62;&#60;&#47;slot&#62; 영역에 나타낼 HTML 마크업을 작성하면 됩니다.

```js
// ChildComponent.vue
<template>
  <div>
    <p>I'm the child component!</p>
    <!-- Content from the parent gets rendered here. -->
    <slot></slot>
  </div>
</template>
```
```js
// ParentComponent.vue
<template>
  <div>
    <child-component>
      <p>I'm injected content from the parent!</p>
      <p>I can still bind to data in the parent's scope, like this! {{myVariable}}</p>
    </child-component>
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent
  },

  data() {
    return {
      myVariable: `I'm just a lonely old variable.`
    }
  }
}
</script>
```
자식 컴포넌트에 &#60;slot&#62;&#60;&#47;slot&#62;이 없을경우 부모로부터 전달되는 콘텐츠는 없어집니다. 반대로, 부모에서 어떠한 콘텐츠도 전달하지 않을경우, &#60;slot&#62;&#60;&#47;slot&#62;에 엘리먼트를 추가하면 기본적으로 랜더링 됩니다.

```js
<slot>
	<p>Hello from the child!</p>
</slot>
```

- 명명된 슬롯

여러 개의 슬롯을 작성할 수 있습니다.

```js
// BurgerComponent.vue
<template>
  <div class="burger-component">
    <!-- Elements injected with the `slot="top-bun"`
    attribute will end up in here. -->
    <slot name="top-bun">
    </slot>
    <!-- A slot tag without a name is a catch-all,
    it will contain any content that doesn't have
    a `slot=""` attribute. -->
    <slot>
    </slot>
    <!-- Elements injected with the `slot="top-bun"`
    attribute will end up in here. -->
    <slot name="bottom-bun">
    </slot>
  </div>
</template>
```

```js
// SecretRecipeBurger.vue
<template>
  <!-- TOP SECRET, FOR EMPLOYEE EYES ONLY -->
  <burger-component>
    <burger-bun slot="top-bun">
      <sesame-seeds></sesame-seeds>
      <mayonaise></mayonaise>
    </burger-bun>
    <burger-bun slot="bottom-bun" :toasted="true">
      <secret-sauce></secret-sauce> <!-- I bought it from some hooded
        guy off the street. -->
    </burger-bun>
    <!-- Everything else gets injected into the middle slot (as it's not named.) -->
    <pickles></pickles>
    <lettuce></lettuce>
    <bacon></bacon>
    <beef-patty></beef-patty>
    <cheese-slice></cheese-slice>
  </burger-component>
</template>
```

**참고**

- https://hackernoon.com/observer-vs-pub-sub-pattern-50d3b27f838c
- https://github.com/vuejs/vue-loader/blob/master/docs/en/features/css-modules.md
- https://css-tricks.com/intro-to-vue-2-components-props-slots/
- https://alligator.io/vuejs/component-slots/
