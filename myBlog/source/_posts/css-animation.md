---
title: CSS 애니메이션(Animation) 실제로 어떻게 쓰나?
date: 2019-07-11 14:18:12
tags:
---

![](/image/css-animation/1.png)
![](/image/css-animation/2.png)
현재, 네이버 예약은 앉은 자리에서 주문과 결제를 한 번에 진행할 수 있는 로컬페이 CBT를 진행중에 있습니다(보시면 많이 이용해주세요^^). 이번 로컬페이 UI개발을 진행하면서 개인적으로 가장 중점을 둔 부분이 애니메이션 입니다. 애니메이션 효과는 사용자로 하여금 인터렉션에 대한 피드백은 물론 소소한 재미를 선사해 줍니다. 아래는 이번 로컬페이에 적용한 애니메이션 화면입니다.

![](/image/css-animation/3.gif)

애니메이션은 크게 세 부분에 적용되어 있습니다.

1. 수량 선택 후, 담기 버튼을 누르면 해당 메뉴 우측에 있는 카트가 초록색으로 변하면서, 수량 표시.
2. 하단 주문하기 버튼의 배경색이 변하면서, 비어 있던 카트가 흰색으로 변하면서 수량 표시와 함께 반동 애니메이션 적용.
3. 테이블주문/포장주문 선택 시, 상단 배경이 바뀌면서 가운데 두 아이콘이 스와이프 형태로 이동.

한 화면에 적용된 애니메이션이 평소 팝업/로딩바에 비하면 상당히 많은 걸 알 수 있습니다.

**animation-timing-function**

![](/image/css-animation/4.png)

UI개발을 진행하면서 웹상에서 애니메이션을 표현하기 위해 가장 기본적인 방법은 연속의 PNG이미지를 만들어서 `animation-timing-function`의 `step-start`를 적용할 수 있습니다. 간단한 방법이지만, 애니메이션의 시퀀스가 길어질수록 PNG이미지 관리가 어렵고 각각의 이동에 대한 `background-position`을 설정해 줘야하기 때문에 사용의 제한이 있습니다. 또한, 색상과 사이즈를 수정하는데 어려움이 있습니다. 그렇기 때문에, 반복하는 짧은 길이의 로딩바를 표현하기에 적당합니다. 연속된 이미지를 하나의 움직이는 파일로 만든게 GIF 파일이며, 이 또한 많이 사용하고 있지만 성능 최적화를 위한 최선의 방법은 아닙니다.

**CSS animation**

![](/image/css-animation/5.gif)

PNG, GIF 파일 등 화면을 구성하는 파일이 많아 질수록 HTTP1.1일 경우 리소스 요청 증가 및 성능 이슈가 생길 수 있습니다. 간단한 로딩바와 같은 애니메이션은 CSS 스타일로 충분히 구현 가능합니다. CSS 스타일로 구현이되어 있기 때문에 색상, 사이즈, 굵기, 추가효과 구현이 용이합니다.

```css
/* 로딩 CSS 애니메이션 구현 */

.loader {
    position: absolute;
    top: 0;
    left: 0;
    margin: 50px auto;
    width: 210px;
    height: 210px;
    border-radius: 50%;
    background: #fff;
    background: -webkit-linear-gradient(left, #00cf5b 10%, rgba(255, 255, 255, 0) 42%);
    background: linear-gradient(to right, #00cf5b 10%, rgba(255, 255, 255, 0) 42%);
    position: relative;
    -webkit-animation: load 1s infinite linear;
    animation: load 1s infinite linear;
    -webkit-transform: translateZ(0);
    transform: translateZ(0);
    font-size: 60px;
    color: transparent;
}
.loader:before {
    width: 100%;
    height: 50%;
    background: #00cf5b;
    border-top-right-radius: 150px;
    border-top-left-radius: 150px;
    position: absolute;
    top: 0;
    left: 0;
    content: '';
}
.loader:after {
    background: #fff;
    width: 95%;
    height: 95%;
    border-radius: 50%;
    content: '';
    margin: auto;
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
}
@keyframes load {
    0% {
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }
    100% {
        -webkit-transform: rotate(360deg);
        transform: rotate(360deg);
    }
}
```

처음 로컬페이 애니메이션 가이드 영상을 받았을 때, 어떤 방법으로 구현이 가능할지 검토를 하였습니다. 우선 연속된 PNG이미지를 이용해서 구현 하기에는 복잡도가 높고, 그라데이션 효과까지 들어가 있어 용량이 생각보다 커질 수가 있습니다. 더불어 디테일한 아이콘은 물론 선택한 수량까지 실시간으로 반영해야해서 100% CSS 애니메이션으로 그려서 적용하는건 더욱더 불가능합니다.

![](/image/css-animation/6.png)

그럼, 디자이너로 부터 움직이는 모든 애니메이션의 GIF파일을 받는다? HTML로 영역만 잡고 전달받은 파일만 추가하면 되기 때문에 가장 간단한 방법입니다. 하지만, 이것역시 가장 좋은 방법은 아닌 것 같다는 생각이 들었습니다. 마지막 남은 방법은 최소한의 아이콘 이미지 + CSS 스타일/애니메이션 이었습니다.

`Lottie in React`도 시험삼아 사용하고 싶었지만, 에프터이펙트를 통해서 JSON파일을 전달 받아야해서 오히려 작업 프로세스가 늘어나서 이번 프로젝트에서는 사용을 못했습니다.

개발자 마다 구현하는 방법과 순서가 다르겠지만, 우선 저는 큰 동작 단위로 애니메이션을 분류해봤습니다.

- 좌우이동
- 이동 시 약간의 회전(기울기)
- 색반전 + 아이콘 변경
- 짧은 막대 효과

총 4가지의 효과를 순서대로 잘 적용을 하면 되겠구나 생각했습니다. 그리고 리액트로 진행된 프로젝트라서 최소한의 state 값으로 애니메이션의 상태를 관리하면 좋을 것 같았습니다. 우선 영역을 잡아 보도록 하겠습니다.

![](/image/css-animation/7.png)

애니메이션이 적용되는 영역을 만들었으니 전달받은 인터렉션 가이드와 최대한 비슷하게 CSS 애니메이션만 적용하면 됩니다! 복잡하게 보이는 애니메이션도 알고보면 간단한 인터렉션의 조합으로 이루어져 있습니다^0^

![](/image/css-animation/8.png)

다음으로 생각할 것은, 인터렉션을 적용하기 위해서 `transition` 또는 `animation`을 선택해야 합니다. 좌우이동같은 경우는 인터렉션이 진행되는 동안 단순히 좌/우 위치값만 변경되기 때문에 `transition`을 사용해야합니다. 반면, 기울기 효과는 (기울기X -> 기울기O -> 기울기X) 세 단계로, 인터렉션이 진행되는 중간에 기울기를 추가해줘야 하기 때문에 `animation`을 사용해야합니다. 이 두 속성만 주의해서 감각적(?)으로 잘 적용하신다면 애니메이션 정복! -끝-