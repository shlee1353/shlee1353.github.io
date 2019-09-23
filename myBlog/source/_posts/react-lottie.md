---
title: 리액트(React) Lottie 애니메이션 적용
date: 2019-09-23 17:52:26
tags:
---

![](/image/react-lottie/1.png)

Lottie는 After Effects 애니메이션을 실시간으로 렌더링하는 iOS, Android 및 React Native 라이브러리입니다. 평소, gif, css 애니메이션을 사용한 결과물보다 더욱더 만족스러운 퀄리티를 만들어 낼 수 있다는 장점을 가지고 있습니다. 또 다른 장점으로는 디자이너가 원하는 완성된 결과물을 바로 적용하기 때문에, 개발자가 애니메이션에 구현에 대한 시간과 노력을 절약할 수 있습니다. 애니메이션에 대한 모든 정보가 JSON 파일로 저장되기 때문에 특별한 프로그램 설치가 필요없습니다.
```jsx
import React, { useState } from 'react';
import Lottie from 'react-lottie';
import ThumbsUp from '../lottie/thumbs-up.json';

const lottieOptions = {
  animationData: ThumbsUp,   
  loop: false,        
  autoplay: false,   
  rendererSettings: {
    className: 'add-class', // svg에 적용
    preserveAspectRatio: 'xMidYMid slice'
  }
};

const ThumbsUpComponent = () => {
	const [isStopped, SetIsStopped] = useState(false);
	const [isPaused, SetIsPaused] = useState(true);

	const onStop = () => {
		SetIsStopped(!isStopped)
	};

	const onPause = () => {
		SetIsPaused(!isPaused)
	};

	return (
		<div className="thumbs-up">
			<Lottie
				options={lottieOptions}
				isStopped={isStopped}
				isPaused={isPaused}
				isClickToPauseDisabled={false}
				style={{width: '300px', height: '300px'}} // svg의 부모 div에 적용
				eventListeners={[
					{
						eventName: 'complete',
						callback: () => console.log('the animation completed'),
					},
				]}
			/>
			<button onClick={onPause}>Play/Pause</button>
		</div>
	)
}

export default ThumbsUpComponent;
```
그럼 어떤 단점을 가지고 있을까요? 오로지 After Effect으로 생성이 가능하기 때문에 진입장벽이 있을 수 있습니다. 애니메이션 오브젝트에만 생성 가능하며, 전체 화면의 트렌지션효과는 적용이 불가능합니다.

![](/image/react-lottie/2.gif)

- https://www.npmjs.com/package/react-lottie
- https://lottiefiles.com/