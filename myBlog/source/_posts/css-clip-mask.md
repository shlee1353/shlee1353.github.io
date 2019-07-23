---
title: CSS속성 Clipping과 Masking 차이점과 특징
date: 2019-07-15 14:24:06
tags:
---

![](/image/css-clip-mask/0.png)

평소 `clipping`과 `masking` 속성의 차이점을 알고 계시나요? CSS 속성을 다시 공부할 때마다, “아, 이런게 있었구나!”할 정도로, 매번 업무에 적용하기에는 특별한 속성인 것 같습니다. 우선, 결론적으로, 두 속성의 차이점은 `clipping `은 **벡터 패스**이고, `masking`은 **래스터 이미지**입니다.

![](/image/css-clip-mask/1.png)

- **벡터(Vector)**는 점과 점 사이의 곡선으로 이미지를 구현하는 방식으로, 기본적인 점의 위치 정보만 저장하기 때문에 파일의 크기가 작습니다. 또한, 이미지 축소/확대 시에도 손상을 주지 않는 장점이 있습니다.
- **래스터(Raster)** 이미지는 정사각형 모양의 픽셀(Pixel) 수백개가 모여 전체 이미지를 구성하는 방식입니다. 한 공간에 사용된 모자이크 픽셀 수에 따라서 이미지의 해상도가 달라집니다.

Clips은 항상 벡터 패스로 이루어져있습니다. 패스의 바깥쪽은 투명도가 적용되며, 안쪽은 불투명 영역입니다.

![](/image/css-clip-mask/2-1.png)

mask는 아래와 같이 그라데이션이 적용된 정사각형을 생각하면 이해가 쉽습니다. 마스크 타입에 따라, 검정색과 흰색 부분에 각각 투명도/불투명도가 적용됩니다.

![](/image/css-clip-mask/2-2.png)

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

`polygon()` 속성을 사용하여 원하는 모양을 만들 수 있는데요, 이것을 좀 더 쉽게 적용하려면 [Clippy](https://bennettfeely.com/clippy/)를 사용하는 것을 추천드립니다. Clippy을 사용하여 네이버 로고가 들어간 간단한 인터렉션을 구현해 보도록 하겠습니다.

![](/image/css-clip-mask/4.gif)

## mask 속성
`Masking`은 시각적으로 엘리먼트(이미지 객체)의 일정/전체 부분을 숨기는데 사용됩니다. mask-image 속성을 기본으로 mask-mode, mask-repeat, mask-position, mask-size 등 다양한 속성값을 가지고 있습니다. 각각의 mask는 **mask layer**를 형성하는데요, 두 가지 타입 중 하나를 선택할 수 있습니다. `alpha` 또는 `luminance` 입니다.

- **Alpha Masks**는 알파채널을 가지고 있으며 알파값은 mask 값으로 사용됩니다. 알파채널의 가장 간단한 예로 검정과 투명 영역이 있는 png 이미지를 예로들 수 있습니다.

![](/image/css-clip-mask/5.png)

알파채널은 투명도 정보를 가지고 있는 픽셀데이터의 집합이며 두 픽셀값이 겹쳐져 만났을 때 픽셀의 컬러값을 결정합니다. 위 이미지경우, 검정색은 1이라는 알파값을 가지고 있으며, 투명영역은 0의 알파값을 가지고 있습니다. 알파값을 가지고 있는 이미지를 mask 속성으로 사용할 경우, 검정색 영역에 엘리먼트가 노출되고 투명영역에는 미노출 됩니다.

![](/image/css-clip-mask/6.png)

- **Luminance Masks**는 이미지의 luminance 값을 사용한다는 것을 제외하고는 alpha mask와 유사합니다. alpha mask의 검정색 부분이 흰색으로 바뀌었다고 생각하면 됩니다.

![](/image/css-clip-mask/7.png)

예를들어, object에 luminance mask를 적용하면, 흰색 부분으로 masked object가 나타나는 것을 확인할 수 있습니다.

![](/image/css-clip-mask/8.png)

png, svg파일을 이용해서 mask를 만들 수도 있지만, `gradient` 효과를 사용하여 좀 더 간편하게 mask를 적용하는 방법에 대해 알아보도록 하겠습니다.

![](/image/css-clip-mask/9.png)

`background-image` 속성에 `linear-gradient`를 적용하면 간단한 mask 효과를 적용할 수 있습니다.

```html
<div class="container">
	<img src="http://lorempixel.com/1920/480/">
</div>
```

```css
.container {
	background-image: -webkit-linear-gradient(270deg, rgba(0, 0, 0, 0) 50%, rgba(0, 0, 0, 1) 95% );
	background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0) 50%, rgba(0, 0, 0, 1) 95% );
}
```

mask효과는 clip보다 자연스러운 화면전환 효과가 가능하기 때문에 인터렉션과 비주얼이 중요시되는 웹사이트 제작에 자주 사용되고는 합니다. mask는 보다 화려한 효과를 동반하기 때문에 브라우저 호환성 체크는 필수입니다. 지금까지 간략하게 Clipping과 Masking애 대해 알아봤습니다:)

