# React
## React란?
React는 페이스북에서 만든 웹 프론트엔드 라이브러리로 컴포넌트라는 개념에 집중되어 있어요
컴포넌트를 사용하면 재사용 가능하며 유지보수성을 높여줘요.
생태계가 엄청 넓고, 사용하는 곳도 많아요. 
React에는 라우터, HTTP 클라이언트, 상태 관리등의 기능은 내장되지 않았어요. 
하지만 Redux나 React router 등 라이브러리를 사용하여 혹은 직접 만들어서 보완할 수 있겠지요

## Virtual DOM
변화가 일어날 때 마다 DOM을 새로 업데이트하면 성능상으로 문제가 생길거에요.
이걸 보완해주는 것이 Virtual DOM 이에요.
변화가 일어나면 DOM에 바로 업데이트가 되는 것이 아닌, Virtual DOM에서 렌더링이 되고, 기존의 DOM과 비교하여 필요한 곳만 업데이트를 해주게되요.

---
## React 사용
리액트를 사용하기 위해서는 React와 Component를 불러와야해요.
import는 무엇을 불러온다는 뜻이에요.
불러온다는 뜻이에요. 그렇군요 멋져요
	import React, { Component } from 'react';
React와 그 내부의 Component를 불러왔어요
이제 컴포넌트를 만들어 볼 거에요
컴포넌트를 만드는 방법은 두가지가 있어요

	class App extends Component {
    
    }
이렇게 클래스를 이용해서 만드는 방법이나,

	const App = (props) => {
    	return (
        	
        );
    }
함수를 이용해서 만드는 방법이 있어요.

함수형 컴포넌트를 사용하면 성능 면에서는 이점을 가질 수 있지만,
state나 Lifecycle API를 사용할 수 없어요.

따라서 state나 Lifecycle API를 전혀 사용하지 않을 때는 함수형 컴포넌트를, 아닌 경우에는 클래스형 컴포넌트를 사용하는 것이 효율적이에요.

그리고 맨 마지막에는

	export default App;
    
를 통해서 컴포넌트를 다른 곳에서 불러와 사용할 수 있도록 해주어야 합니다.