---
title: 리액트(React) UI 컴포넌트 설계하기
date: 2019-07-10 15:19:24
tags:
---

![](/image/react-ui-components/1.png)

# 디자인&인터렉션

UI개발을 하시면서 많이 접하시는 디자인이며 간단한 인터렉션과 노출조건이 들어가 있습니다. 하나는 상품을 확인할 수 있는 캐로셀 UI 컴포넌트이며, 다른 하나는 각 이미지에 설명이 들어가 있는 리스트 구조의 UI입니다. 초록색 박스로 그러져있는 설명 부분은 일정 높이에서 펼치기 버튼이 노출됩니다.

![](/image/react-ui-components/2.png)

우선, 매우 간단한 구조이며, 기존에 리액트로 업무를 하시면서 setState 메소드와 간단한 반복문(map)을 사용하셨다면 쉽게 구현이 가능합니다. 저는 다음과 같은 프로세스로 작업을 진행하였습니다.

## 캐로셀

- map을 사용하기 위해 필요한 데이터는 무엇이 있는가? 기본적으로 썸네일에 사용되는 상품 이미지가 있습니다.
- 다음으로, 상품 이미지 경로가 들어가 있는 초기값을 선언해 놓습니다. 더미 데이터를 만드는 방법은 여러가지가 있지만, 우선 간단 예로 3개 정도만 만들어 보도록 하겠습니다.

```js
const initialState = [
    {
        id: 1
        imgUrl: '이미지주소'
    },
    {
        id: 2
        imgUrl: '이미지주소'
    },
    {
        id: 3
        imgUrl: '이미지주소'
    }
]
```

초기값을 담고 있는 initialState 배열을 실제로 사용해야할 변수에 담아줘야 합니다. 기존에는 이 단계에서 setState 메소드를 사용하였지만, 지금은 useState 를 사용합니다.

```js
const [products, setProducts] = useState(initialState)
```

- useState 메소드 인자로 initialState를 넘겨주며 이 값은 products에 담기게 됩니다.
- 이제 기존처럼 map을 사용하여 캐로셀에 들어가는 상품 이미지가 담긴 UI를 생성할 수 있습니다. 생각보다 쉽게 적용이 가능합니다.

```js
const Product = ({ imgUrl }) => {
    
return (
        <li className="item">
            <a href="#" className="product">
                <img src={imgUrl} className="img" width="100" height="100" alt="이미지" />
            </a>
        </li>	
    )
}
<ul className="product_list">
    {products.map(product => (
        <Product
            key={product.id}
            imgUrl={product.imgUrl}
        />
    ))}
</ul>
```

## 리스트
사실, 리스트와 캐로셀을 작업하는데 큰 차이는 없습니다. 다만, 조건식이 더해졌을 때, UI개발에서 어느정도 수준의 인터렉션 개발이 가능한지 보여드리려고 합니다. 복잡한 인터렉션이 아니라면 간단한 것은 직접 개발을 하면 좋을 것 같습니다. 물론, 실제 개발을 진행하면서 수정/삭제 될 수는 있지만, 리액트와 같은 자바스크립트 프레임워크/라이브러리를 이해하는데 큰 도움이 됩니다.

- 초록색 박스 설명의 내용이 두 줄 이상일 경우 height:40px 펼치기 버튼이 노출된다.
- 펼치기 버튼을 누르면 해당 초록색 박스의 높이값이 auto가 되면서 펼쳐진다.

![](/image/react-ui-components/3.png)
예로, 이미지 리스트를 만들기 위해 아래와 같은 인터렉션 조건이 있습니다.

위와같은 인터렉션 조건을 코드로 어떻게 풀어나갈 수 있을까요? 다음과 같은 순서로 생각할 수 있습니다.

1. 펼치기 버튼은 첫 랜더링시 결정되어 사용자에게 보여줘야합니다.
2. 그러기 위해서는, 초록색 설명 박스의 높이값을 알고 있어야합니다.
3. 이때 사용할 수 있는 것이 `useRef` 이며, 이는 jQuery의 선택자와 같은 기능을 합니다.
4. useRef로 초록색 설명 박스를 선택하고, 기존에 사용하였던 `componentDidMount` 대신에 `userEffect`를 사용하여 각각의 설명박스 높이값을 저장합니다.
5. 저장된 높이값을 기준으로 height:40px 이상일 경우 펼침 버튼을 노출하는 조건문을 추가합니다.
6. 펼침 버튼을 눌렀을 시 height값을 auto로 바꿔주는 것은 useRef로 선택한 엘리먼트에 기존처럼 open 클래스를 추가하면 됩니다. 지금까지 설명한 내용을 코드로 살펴보도록 하겠습니다.

```js
const [infoHeight, setInfoHeight] = useState([])
const elRef = useRef([])
function handleClick(e, i) {
    e.preventDefault()
    elRef.current[i].classList.add('open')
}
useEffect(() => {
    setInfoHeight(elRef.current.map(item => item.clientHeight))
}, [])
const Image = () => {
    
return (
        <li className="item">
            <div className="image_box">이미지</div>
            <div className="info_box" ref={el => elRef.current[i] = el}>설명</div>
            {infoHeight[i] >= 40 &&
                <a href="#" className="btn" roel="button" onClick={e => handleClick(e, i)}>펼침버튼</a>
            }
        </li>	
    )
}
<div className="image_area">
    <ul className="image_list">
        {images.map((image, index) => {
            <Image
                key={index}
            />
        }}
    </ul>
</div>
```

갑자기 코드량이 많아졌네요! 코드를 한 줄씩 살펴보도록 하겠습니다.

1. `div.info_box`에 `ref={el => elRef.current[i] = el}`를 추가합니다. 여기서 중요한 것은 useRef 메소드를 통해서 `elRef` 객체를 생성하면 current 값이 자동으로 생성되며 여기에 선택된 엘리먼트를 순서대로 추가하는 코드입니다.
2. 그런다음 `useEffect`를 통하여 저장된 초록박스 설명의 높이값을 가져와서, 상단에 이미 선언해 놓은 infoHeight 배열에 추가합니다.
3. 이제 배열 안에는 초록박스의 높이값이 전부 담겨 있으며 `infoHeight[i] >= 40` 조건문을 통하여 펼침버튼 노출/미노출을 결정하게됩니다.
4. 펼침 버튼에는 `onClick={e => handleClick(e, i)}`를 추가하여 클릭시 해당 초록박스에 open 클래스를 추가하여 높이값을 auto로 만들어줍니다. 이부분은 css에 미리 선언해 두셔야합니다.

**실제 서비스 적용 화면**

![](/image/react-ui-components/4.gif)

처음 리액트로 서비스를 시작하시는 분들에게는 다소 복잡한 내용일 수 있지만, 컴포넌트 구조화 및 기본적인 인터렉션만 잘 구현해도 FE협업시 큰 시너지 효과가 있다고 생각합니다.