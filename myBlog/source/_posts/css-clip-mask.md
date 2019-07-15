---
title: CSS속성 Clipping과 Masking 차이점과 특징
date: 2019-07-15 14:24:06
tags:
---


평소 `clipping`과 `masking` 속성의 차이점을 알고 계시나요? 맨 처음 CSS 속성을 공부할 때, “아, 이런게 있구나!”할 정도로, 매번 업무에 적용하기에는 특별한 속성인 것 같습니다. 우선, 결론부터 말씀드리면, 두 속성의 차이점은 `clipping `은 **벡터 패스**이고, `masking`은 **래스터 이미지**입니다.

![](/image/css-clip-mask/1.png)

- **벡터(Vector)**는 점과 점 사이의 곡선으로 이미지를 구현하는 방식으로, 기본적인 점의 위치 정보만 저장하기 때문에 파일의 크기가 작습니다. 또한, 이미지 축소/확대 시에도 손상을 주지 않는 장점이 있습니다.
- **래스터(Raster)** 이미지는 정사각형 모양의 픽셀(Pixel) 수백개가 모여 전체 이미지를 구성하는 방식입니다. 한 공간에 사용된 모자이크 픽셀 수에 따라서 이미지의 질이 달라집니다.

예를들어, 아래와 같이 그라데이션이 추가된 정사각형을 마스크로 정의할 수 있습니다. 좌측의 검정색 부분은 투명도가 적용되며, 우측의 흰색 부분은 불투명도가 적용됩니다.

![](/image/css-clip-mask/2.png)


Clips은 항상 벡터 패스로 이루어져있습니다. 패스의 바깥쪽은 투명도가 적용되며, 안쪽은 불투명 영역입니다.

## clip 속성

우선 가장 기본적인 clip 속성에 대해 알아보도록 하겠습니다.

```css
.element {
  clip: rect(10px, 20px, 30px, 40px); // 상 우 하 좌
}
```

clip 속성은 현재 `deprecated` 되었으며, 새로운 속성이 추가되어 더 이상 추천하지 않습니다. 또한, clip은 쉽게 사용하기 어려운 두 가지 제약이 있습니다.

- 엘리먼트에 `absolute` 속성이 적용되어야 합니다.
- 직사각형 형태로만 적용 가능합니다.

## clip-path

`clip-path`는 새롭게 추가된 속성으로 여러 형태의 도형을 적용할 수 있다는 장점이 있습니다. 물론, 다양한 도형을 적용하려면 브라우저 호환성을 꼭 확인해야 합니다.

```css
.clip-circle {
  clip-path: circle(40px at center);
}
.clip-ellipse {
  clip-path: ellipse(130px 140px at 10% 20%);
}
.clip-polygon {
  clip-path: polygon(50% 0, 100% 50%, 50% 100%, 0 50%);
}
```

![](/image/css-clip-mask/3.png)

`polygon()` 속성을 사용하여 원하는 모양을 만들 수 있는데요, 이것을 좀 더 쉽게 적용하려면 [Clippy](https://bennettfeely.com/clippy/)를 사용하는 것을 추천드립니다. Clippy의 도움을 받아 네이버 로고가 들어간 간단한 인터렉션을 구현해 보도록 하겠습니다.

![](/image/css-clip-mask/4.gif)

## mask 속성