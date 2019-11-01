---
title: 래스터(Raster) 이미지와 백터(Vector) 이미지 for CSS 속성
date: 2019-09-26 10:31:11
tags:
---
![](/image/css-raster-vector/0.png)

![](/image/css-raster-vector/1.png)

일상속에서 휴대폰으로 찍은 사진은 어떤 이미지 타입으로 저장이 될까요? 이 물음에 답하기 위해서는 먼저 이미지 파일에 대한 기본적인 이해가 필요합니다. 우리가 컴퓨터 모니터 상에서 사용하는 그래픽은 크게 `Raster`와 `Vector` 두 가지 타입으로 존재합니다. 많은 이미지들이 이 두가지 형태로 생성되며 때로는 매우 비슷하거나 동일하게 보일때도 있습니다. 하지만, 이 둘은 분명한 차이점이 존재하며, 용도에 맞게 사용해야할 필요가 있습니다.

이번 포스팅에서는 `Rster`와 `Vector` 그래픽의 특징을 알아보고, 이 두 그래픽 타입을 기반으로하는 CSS속성인 `Masking`과 `Clipping`을 설명하고자합니다.
***

![](/image/css-raster-vector/2.png)


`Raster`의 또 다른 명칭은 `비트맵(bitmaps)`이며 `RGB(red, green, blue)` 색상값을 가진 하나의 픽셀(점)들이 모여 이미지를 만들어냅니다. 작은 픽셀들이 모여 이미지를 화면에 출력하기 때문에 생생하고 세밀한 표현이 가능하다는 장점을 가지고 있습니다. 그렇기 때문에, 디지털화된 사진이나 미술작품처럼 풍부한 컬러감과 자연스러운 그라데이션이 필요한 곳에 주로 사용되며, `PNG, JPG, GIF` 파일이 여기에 속합니다. 웹상에서는 간단한 아이콘, 사진 또는 반복되는 이미지를 보여줄 때 주로 사용합니다.

![](/image/css-raster-vector/3.png)

`Raster`이미지의 선명도는 `PPI(Pixels-per-inch)`로 나타내며 이는 1인치 정사각형안에 몇 개의 픽셀을 담고 있는지를 나타냅니다. 예를들어, 1 PPI는 1x1=1개, 2 PPI는 2x2=4개, 4 PPI는 4x4=16개의 픽셀로 이루어져 있으며, 인쇄물 용도로 제작하는 이미지는 최소 300PPI를 요구합니다. 여기서 중요한점은, 높은 PPI로 이미지를 제작했더라도 그것을 출력하는 디바이스 해상도와는 아무런 관련이 없다는 것입니다. 오히려, PPI는 이미지 혹은 사진 파일을 프린터로 출력할 때 고려해야 할 부분입니다.

![](/image/css-raster-vector/4.png)

디바이스의 해상도를 나타낼 때는 주로 `1280x720, 1920x1080` 표기를 사용하며, 이는 가로와 세로 화면에 들어가는 총 픽셀수를 의미합니다. 화면상에서 이미지의 사이즈를 결정짓는건 단 두 가지가 있습니다. 바로, **이미지의 실제 넓이와 높이**`(pixel dimensions)`, 그리고 **디바이스의 해상도**입니다.

![](/image/css-raster-vector/5.png)

예를들어, `440x550` 사이즈로 제작된 캐릭터 이미지가 있다고 하면, 모니터의 실제 사이즈는 동일하지만, 해상도가 높은 화면에서 출력할 경우 더욱더 작게 보여 선명한 상태로 인식하게 됩니다. PPI를 아무리 높여도 보이는 결과는 달라지지 않습니다. 고해상도의 이미지를 출력할 경우 높은 PPI는 필요조건이지만 그만큼 이미지 파일의 사이즈는 커지게 됩니다. 또한, 일정 픽셀 안에 고정된 컬러값과 사이즈를 가지고 있기 때문에 이미지를 확대할 경우 깨짐 현상이 발생합니다. 이러한 단점들을 보완한 것이 바로 `Vector`기반 이미지입니다.

![](/image/css-raster-vector/6.png)

`Vector` 그래픽은 기본적으로 `꼭지점과 연결된 선(vertices and paths)`으로 구성되어 있으며 이를 사용하여 사각형, 다각형, 원 등과 같은 다양한 도형들을 만들어냅니다. 기본적으로, 하나의 `점(point)`으로 시작되며 두 개의 점을 선으로 이으면 이것을 `path`라고 부릅니다. 여러 꼭지점으로 연결된 paths의 한 곳이 닫히면 `기하학적 패턴 또는 도형`이 완성됩니다.

![](/image/css-raster-vector/7.gif)

각각의 `point`는 x, y 좌표값을 가지고 있으며, `path`에는 선의 색상, 모양, 굵기 등 다양한 정보를 담고 있습니다. 그렇기 때문에, 백터 그래픽으로 그려진 이미지는 확대를 하여도 고유 정보를 담고 있기 때문에 깨지지 않으며, 대표적으로 `SVG(Scalable Vector Graphics)`파일이 해당됩니다. 다양한 디스플레이에서도 이미지를 선명하게 노출 시킬 수 있기 때문에, SVG는 웹에서 반응형 디자인을 고려시 가장 적합한 그래픽 포멧입니다.

![](/image/css-raster-vector/8.png)

```js
// 최적화 전: 36KB
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 864 864" enable-background="new 0 0 864 864" xml:space="preserve">

// 최적화 후: 20KB
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 864 864">

```
또한, 불필요한 점과 선을 제거함으로써, 이미지 최적화를 통한 저용량 파일 생성이 가능합니다. 예로, [SVGOMG](https://jakearchibald.github.io/svgomg/)를 사용하면 SVG코드 내부에 담겨 있는 불필요한 정보를 손쉽게 제거하여 파일 용량을 줄일 수 있습니다.

![](/image/css-raster-vector/9.jpg)

지금부터는 CSS 속성 중에서 `래스터(Raster)`와 `벡터(Vector)` 그래픽을 기반으로 하는 `masking`과 `clipping` 에 대해 알아보도록 하겠습니다. `mask`는 래스터 그래픽을 사용하며, `clip`은 백터 패스가 적용되는 효과입니다. `Mask`는 그라데이션이 적용된 정사각형을 생각하면 이해가 쉽습니다. `마스크 타입(Alpha 또는 Luminance)`에 따라, 검정색과 흰색 부분에 각각 투명도/불투명도가 적용됩니다. `Clip`은 벡터 패스 바깥쪽은 투명도가 적용되며 안쪽은 불투명 영역을 이루고 있습니다.

![](/image/css-raster-vector/10.png)

이 두 속성은 특별한 경우가 아니면 다른 속성들에 비해 실무에서 많이 사용되지는 않습니다. 하지만, 각 속성에 대한 기본적인 특징과 사용방법을 잘 숙지하여 적재적소에 활용한다면 더욱더 효과적인 인터렉션 구현이 가능합니다. 두 속성에 대해 좀 더 자세히 알아보도록 하겠습니다.

## Mask 속성

`Masking`은 시각적으로 엘리먼트(이미지)의 일정/전체 부분을 숨기는데 사용됩니다. Mask-image속성을 기본으로 mask-mode, mask-repeat, mask-position, mask-size 등 다양한 속성값을 가지고 있습니다. mask를 사용하면 `mask layer`가 형성되며, `alpha` 또는 `luminance` 두 가지 타입 중 하나를 선택할 수 있습니다. 두 속성의 차이점은 aphla는 투명도를 만들어 내기 위해 `알파채널(alpha channel)`을 사용하며, luminance는 `밝기값(lightness values)`을 사용한다는 점입니다.

- `Alpha Masks`는 알파채널을 가지고 있으며 알파값은 mask 값으로 사용됩니다. 알파채널의 가장 간단한 예로 검정과 투명 영역이 있는 png 이미지를 예로들 수 있습니다.

![](/image/css-raster-vector/11.png)

알파채널은 투명도 정보를 가지고 있는 픽셀데이터의 집합이며 두 픽셀값이 겹쳐져 만났을 때 픽셀의 컬러값을 결정합니다. 위 이미지경우, 검정색은 1이라는 알파값을 가지고 있으며, 투명영역은 0의 알파값을 가지고 있습니다. 알파값을 가지고 있는 이미지를 mask 속성으로 사용할 경우, 검정색 영역에 엘리먼트가 노출되고 투명영역에는 미노출 됩니다.

![](/image/css-raster-vector/12.jpg)

- `Luminance Masks`는 이미지의 luminance 값을 사용한다는 것을 제외하고는 alpha mask와 유사합니다. alpha mask의 검정색 부분이 흰색으로 바뀌었다고 생각하면 됩니다.

![](/image/css-raster-vector/13.png)

예를들어, object에 luminance mask를 적용하면, 흰색 부분으로 masked object가 나타나는 것을 확인할 수 있습니다.

![](/image/css-raster-vector/14.png)

PNG, SVG 파일을 사용해서 mask를 구현할 수도 있지만, `gradient` 효과를 사용해서 CSS에서 직접 구현이 가능합니다. 더 나아가, CSS애니메이션과 mask를 적절히 사용하면 부드러운 화면 전환을 보여주는 간단한  애니메이션 구현도 가능합니다.

![](/image/css-raster-vector/15.png)

```html
<div class="container">
	<img src="https://amp.thenational.ae/image/policy:1.164632:1499296962/image/jpeg.jpg?f=16x9&w=1200&$p$f$w=dfa40e8">
</div>
```

```css
.container {
  -webkit-mask-image: linear-gradient(to top, transparent 25%, black 75%);
}
```

![](/image/css-raster-vector/16.gif)

## clip 속성

백터(Vector) 기반인 clip 속성은 기본적으로 정사각형의 기본값을 가지고 있습니다. clip 속성은 현재 `deprecated` 되었으며, 새로운 속성이 추가되어 더 이상 추천하지 않습니다. 또한, clip은 쉽게 사용하기 어려운 두 가지 제약이 있습니다.

- 엘리먼트에 `absolute` 속성이 적용되어야 합니다.
- 직사각형 형태로만 적용 가능합니다.

```css
.element {
  clip: rect(10px, 20px, 30px, 40px); // 상 우 하 좌
}
```

**clip-path**

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

![](/image/css-raster-vector/17.png)

`polygon()` 속성을 사용하여 원하는 모양을 만들 수 있으며, 이것을 좀 더 쉽게 적용하려면 [Clippy](https://bennettfeely.com/clippy/)를 사용하는 것을 추천드립니다. Clippy을 사용하여 네이버 로고가 들어간 간단한 인터렉션을 구현해 보도록 하겠습니다.

![](/image/css-raster-vector/18.gif)

컴퓨터 그래픽에서 `Rster`와 `Vector`은 매우 중요한 요소 이기 때문에 기본적인 개념을 정리하여 공유하면 좋을 것 같아서 이번 포스팅을 작성하였습니다. 특히, `Masking`, `Clipping` 과 같은 CSS속성에도 사용할 수 있을 정도로 활용 범위가 넓은 만큼 앞으로 진행하는 프로젝트에서 적재적소에 사용하면 좋을 것 같습니다.