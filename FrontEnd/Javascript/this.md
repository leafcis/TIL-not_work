# this

자바스크립트의 함수는 호출될 때, 매개변수로 전달되는 인자값 이외에, arguments  객체와 this를 전달 받는데, 자바스크립트의 this는 타 언어와 개념이 다릅니다.

일반적으로 자바나 c++ 같은 경우에는 this가 객체 자신에 대한 참조 값을 가지나,

~~~c++
class Sample {
	int test = 0;
	public:
		void SampleFunction(int test) {this->test = test;} //this->test는 해당 객체의 															test를 가리킵니다
};
~~~

자바스크립트는 아닙니다. 자바스크립트는 this에 바인딩되는 객체가 한가지가 아닌, 해당 함수 호출 방식에 따라 this에 바인딩되는 객체가 달라집니다.



## 함수 호출 방식과 this 바인딩

그럼 뭡니까? 궁금하네요 뭐 어찌 바인딩 된다는 소립니까?

성질 급하시네요.

자바스크립트의 경우 함수 호출 방식에 의해 this에 바인딩할 객체가 동적으로 결정된답니다. 즉, 함수를 선언할 때 this에 바인딩 할 객체가 정적으로 결정되는 것이 아니고, 함수를 호출할 때 함수가 어떻게 호출되었는지에 따라 this에 바인딩할 객체가 동적으로 결정됩니다.

> 여담이나, 함수의 상위 스코프를 결정하는 렉시컬 스코프는 함수를 선언할때 결정됩니다.  this 바인딩과 혼동하지 마세요

야 그럼 함수 호출 방식이 뭐 있다고 그거에 의해 결정되냐?

>1. 기본적인 함수 호출
>2. 메소드 호출
>3. 생성자 함수 호출
>4. apply, call, bind 호출

함수 호출 방식들은 이렇게 다양합니다.

~~~javascript
var foo = function() {
    condole.dir(this);
};

// 1. 함수 호출
foo(); // window
// window.foo();

// 2. 메소드 호출
var obj = { foo : foo };
obj.foo(); // obj

// 3. 생성자 함수 호출
var instance = new foo(); // instance

var bar = { name : 'bar' };
foo.call(bar); // bar
foo.apply(bar); // bar
foo.bind(bar))(); //bar
~~~



## 1. 함수 호출

전역객체는 모든 객체의 유일한 최상위 객체를 의미하며 일반적으로 Browser-side에서는 window, Server-side(Node.js)에서는 global 객체를 의미합니다.

~~~js
// 브라우저
this === window // true

// 터미널
node
this === global // true
~~~

전역객체는 전역 스코프를 갖는 전역변수를 프로퍼티로 소유하며, 글로벌 영역에 선언한 함수는 전역 객체의 프로퍼티로 접근할 수 있는 전역 변수의 메소드입니다.



~~~javascript
var ga = '전역 변수';

console.log(ga);
console.log(window.ga);

function foo() {
  console.log('foo!');
}
window.foo();
~~~

기본적으로  this는 전역객체에 바인딩되며, 전역함수는 물론이고 심지어 내부함수의 경우도 this는 외부함수가 아닌 전역객체에 바인딩됩니다.



~~~javascript
function foo() {
    console.log("foo's this is ", this); // window
    function bar() {
        console.log("bar's this is ", this); //window
    }
    bar();
}
foo();
~~~

또한 메소드의 내부함수일 경우에도 this는 전역객체에 바인딩됩니다.

~~~javascript
var value = 1;

var obj = {
  value: 100,
  foo: function() {
    console.log("foo's this is ",  this);  // obj
    console.log("foo's this.value is ",  this.value); // 100
    function bar() {
      console.log("bar's this is ",  this); // window
      console.log("bar's this.value is ", this.value); // 1
    }
    bar();
  }
};

obj.foo();
~~~

콜백함수의 경우에도 this는 전역객체에 바인딩됩니다.

~~~javascript
var value = 1;

var obj = {
  value: 100,
  foo: function() {
    setTimeout(function() {
      console.log("callback's this is ",  this);  // window
      console.log("callback's this.value is ",  this.value); // 1
    }, 100);
  }
};

obj.foo();
~~~

내부함수는 일반 함수, 메소드, 콜백함수 어디에서 선언되었든 관계없이 this는 전역객체를 바인딩합니다.

내부함수의 this가 전역객체를 참조하는 것을 회피하는 방법도 있습니다.



~~~javascript
var value = 1;

var obj = {
  value: 100,
  foo: function() {
    var that = this;  // Workaround : this === obj

    console.log("foo's this: ",  this);  // obj
    console.log("foo's this.value: ",  this.value); // 100
    function bar() {
      console.log("bar's this: ",  this); // window
      console.log("bar's this.value: ", this.value); // 1

      console.log("bar's that: ",  that); // obj
      console.log("bar's that.value: ", that.value); // 100
    }
    bar();
  }
};

obj.foo();
~~~

이렇게 메소드 내에 this를 변수에 저장해 둔 다음, 이를 내부함수에서 사용해주면 됩니다.

이 방법 외에 자바스크립트는 this를 명시적으로 바인딩할 수 있는 메소드를 제공합니다.

apply, call, bind 이렇게 셋이죠.

~~~javascript
var value = 1;

var obj = {
    value: 100,
    foo: function() {
        console.log("foo's this is ", this);
        console.log("foo's this is ", this.value);
        function bar(a, b) {
            console.log("bar's this is ", this);
            console.log("bar's this.value is ", this.value);
            console.log("bar's arguments is ", arguments);
        }
        bar.apply(obj, [1, 2]);
        bar.call(obj, 1, 2);
        bar.bind(obj)(1,2);
    }
};

obj.foo();
~~~

이들은 후술하여서 설명하겠습니다.



## 2. 메소드 호출

함수가 객체의 프로퍼티 값이면 메소드로서 호출됩니다. 이때 메소드 내부의 this는 해당 메소드를 소유한 객체, 즉 해당 메소드를 호출한 객체에 바인딩됩니다.



~~~javascript
var obj1 = {
    name: 'Lee',
    sayName: function() {
        console.log(this.name);
    }
}

var obj2 = {
    name: 'Kim'
}

obj2.sayName = obj1.sayName;

obj1.sayName();
obj2.sayName();
~~~

프로토타입 객체도 메소드를 가질 수 있어요. 프로토타입 객체 메소드 내부에서 사용된 this도 일반 메소드 방식과 마찬가지로 해당 메소드를 호출한 객체에 바인딩됩니다.



~~~javascript
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function() {
  return this.name;
}

var me = new Person('Lee');
console.log(me.getName());

Person.prototype.name = 'Kim';
console.log(Person.prototype.getName());
~~~

 

