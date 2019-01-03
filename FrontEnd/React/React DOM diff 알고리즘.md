# React DOM diff 알고리즘

React를 사용할 때, **render()** 함수는 react의 새로운 엘리먼트를 반환합니다. 또 state나 prop이 바뀌어서 render() 단계에서 다시 새로운 엘리먼트를 반환하기도 하구요.

여기서 새롭게 렌더링이 될 때, react는 **DOM diff 알고리즘**을 이용해 효율적으로 UI를 업데이트 하게 됩니다.

일반적으로 사용되는 트리를 다른 트리로 바꾸는 알고리즘은 O(n<sup>3</sup>)의 시간복잡도를 가지게 됩니다. 정말 굉장하죠?

그리고 다들 아시겠지만 component내의 render()의 결과는 실제 DOM이 아닙니다. 그리고 Reactdom.render() 함수의 결과 또한 실제 DOM이 아니구요. Reactdom.render()의 결과는 Virtual DOM을 생성해내고, 이 Virtual DOM을 DOM으로 변환? 한다는 개념은 Reconciliation 이라고 합니다. 화해라는 뜻이죠.



React DOM diff 알고리즘의 시간복잡도는 O(n)의 시간복잡도를 가집니다.

## Dom diff 알고리즘

Dom diff 알고리즘이 적용되어 렌더링 되기 위한 조건들은 다음과 같습니다.

### 다른 타입의 요소일 때

렌더링된 DOM 트리가 전의 것과 비교해서 다른 타입의 요소일 때에, 전의 것은 없어지고 새로운 것을 완전히 새롭게 렌더링합니다.

~~~react
<!-- 변경 전 -->
<div>
	<Hello />
</div>

<!-- 변경 후 -->
<span>
	<Hello />
</span>
~~~

예를 들어, 위의 경우에는 `div`와 `span`이 다른 타입이라, 기존 `Hello` 컴포넌트는 언마운트 되고, 새로운 `Hello` 컴포넌트가 마운트 되게 됩니다.



### 같은 타입의 DOM 엘리먼트이나 property가 달라질 때

일단 attribute와 propery의 차이를 말해보고 싶습니다. attribute는 html document 안에서, property는 html DOM tree안에 존재함으로, attribute는 정적이고, property는 동적인 성질을 가지고 있습니다.  뭔 소리냐구요?

~~~html
<!-- HTML -->
<input value = "">

<!-- Attribute : "" -->
<!-- Property : "" -->
<!-- value : "" -->

<!-- input에 텍스트 값을 입력 시 -->

<!-- HTML -->
<input value = "">

<!-- Attribute : "" -->
<!-- Property : "안녕하세요" -->
<!-- value : "안녕하세요" -->

~~~

이처럼 DOM 값이 바뀜에 따라 property도 바뀌게 됩니다.

본론으로 넘어가 property가 변경된 같은 타입의 DOM 엘리먼트(`div`, `a`, `img` 등) 일 때에 react가 그 엘리먼트의 property의 바뀐 부분만 감지하여 변경하게 됩니다.

~~~react
<!-- 변경 전 -->
<div className = "b" title = "stuff" />

<!-- 변경 후 -->
<div className = "a" title = "stuff" />
~~~

이와 같은 경우에 react는 div의 className이 변경된 것을 감지하고, 전체를 바꾸지 않고 className만 변경하게 됩니다.

마찬가지로

~~~react
<!-- 변경 전 -->
<div style = { color: black, fontWeight : 20px } />

<!-- 변경 후 -->
<div style = { color: white, fontWeight : 20px } />
~~~

와 같은 경우에는 style 전체가 바뀌지 않고, color만 변경되게 됩니다.



### 같은 타입의 컴포넌트 엘리먼트일 때

#### 자식 엘리먼트 순회

React는 자식 엘리먼트를 순회하면서 차이가 있을 때마다 변경합니다.

~~~react
<!-- 변경 전 -->
<div>
	<span>1</span>
	<span>2</span>
</div>

<!-- 변경 후 -->
<div>
	<span>1</span>
	<span>2</span>
	<span>3</span>
</div>
~~~

엘리먼트 트리의 변화가 위와 같다면 react는 `<span>1</span> `을 비교하고, `<span>2</span>`을 비교하고 세번째에서 `<span>3</span>`을 삽입할 것입니다.

허나, 

~~~react
<!-- 변경 전 -->
<div>
	<span>1</span>
	<span>2</span>
</div>

<!-- 변경 후 -->
<div>
	<span>0</span>
	<span>1</span>
	<span>2</span>
</div>
~~~

와 같이 새로운 엘리먼트가 삽입된다면 react는 모든 자식 엘리먼트를 변경함으로써 좋지 못한 성능을 자랑할 것입니다.



#### Key

이러한 문제를 react는 친히 해결해 주노라 하고 `key`attribute를 만들어 주었습니다. react는 `key`를 통해서 엘리먼트를 비교하게 됩니다.

~~~react
<!-- 변경 전 -->
<div>
	<span key = "1">1</span>
	<span key = "2">2</span>
</div>

<!-- 변경 후 -->
<div>
	<span key = "0">0</span>
	<span key = "1">1</span>
	<span key = "2">2</span>
</div>
~~~

위와 같이 `key`를 attribute로 주면 react는 `아! <span key = "0">0</span> 이 새로 추가되었구나!`를 알 수 있을 것입니다. 없던 `key`였으니까요.

`key`는 부모 엘리먼트 내에서만 유일하면 되며, 되도록 안정적인 값을 사용하길 바랍니다. 상식적으로 생각해도 유일하며, 중요한 `key`를 랜덤으로 만들면 안되겠죠?