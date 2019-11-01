---
title: 자바스크립트 이벤트 버블링 캡쳐링 완벽정리
date: 2019-11-07 16:14:11
tags:
---

![](/image/js-bubbling-capturing/0.jpg)

자바스크립트 이벤트 버블링/캡처링은 이벤트 전달/차단과 관련해서 매우 중요합니다. 우선 간단한 예를 살펴 보도록 하겠습니다.

```html
<div onclick="alert('The handler!')">
  <em>click</em>
</div>
```
다음 코드를 실행하면 클릭 시 `<div>`에 있는 `alert()` 함수가 실행됩니다. 왜일까요?

### 버블링(Bubbling)

버블링은 매우 간단합니다. 엘리먼트에서 이벤트가 감지 되었을 때, 해당 엘리먼트를 포함하고 있는 부모 엘리먼트를 통하여 최상위 까지 이벤트가 전달되는 것을 버블링이라고 합니다.

```html
<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>
```

위 코드를 실행하면 차례대로, `p > div > form` 창이 뜨게됩니다. 대부분은 이벤트는 버블링을 실행합니다. 예외적으로, focus는 버블링이 없습니다.

### event.target

부모의 선언된 이벤트 핸들러는 어디서 이벤트가 발생 했는지 알 수 있습니다. `event.target`은 최초 이벤트가 발생하는 엘리먼트를 가리키며 `event.currentTarget`은 실제로 이벤트가 실행되는 엘리먼트를 알려줍니다.

`event.target`은 이벤트 버블링의 가장 마지막에 위치한 요소를 반환합니다. 즉, 클릭된 요소를 기준으로 사용할 경우 `event.target`을 사용합니다. `event.currentTarget`은 실제로 이벤트가 바인딩 당하는 요소를 반환합니다.

```html
<div class="yellow" id="yellow" style="background: #ff0; width: 300px; height: 150px">
    <div class="blue" style="background: blue; width: 200px; height: 100px"></div>
</div>

<script type="text/javascript">
var divElement = document.getElementById('yellow');
divElement.onclick = function(e){
    e = e || window.event;
    var target = e.target || e.srcElement;
    var current = e.currentTarget || this;
    alert('target: '+target.className + ' currentTarget: ' + current.className);
}
</script>
```
### Stopping bubbling

버블링 이벤트는 타깃 엘리먼트에서 위로 올라갑니다. 일반적으로 `html` 태그까지 올라가며 최종적으로 document객체에 도달합니다. 몇몇 이벤트는 `window`까지 전달됩니다. 이를막기위한 방법이 `event.stopPropagation()` 메소드가 있습니다.

```html
<div onclick="alert('hi')">
  <em onclick="event.stopPropagation()">click</em>
</div>
```

처음 코드와 동일하지만 버블링을 막으니 `<div>`에 선언된 이벤트가 실행되지 않습니다. 만약 엘리먼트에 여러개의 이벤트가 걸려 있다면 `event.stopImmediatePropagation()`을 통해 전체를 막을 수 있습니다.

### 캡처링(Capturing)

캡처링은 window부터 최초 이벤트가 발생한 자식 요소로 내려가는 과정을 말합니다. 버블링에 비해 사용 빈도가 적으며 `addEventListenr()`의 세 번째 파라미터(true/false)를 통해 캡처링 or, 버블링 단계를 확인할 수 있습니다.