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

 

## 3. 생성자 함수 호출

자바스크립트의 생성자 함수는 말 그대로 객체를 생성하는 역할을 합니다. 

하지만 자바와 같은 객체지향 언어의 생성자 함수와는 다르게 그 형식이 정해져 있는 것이 아니라 기존 함수에 new 연산자를 붙여서 호출하면 해당 함수는 생성자 함수로 동작합니다.



~~~javascript
function Person(name) {
    this.name = name;
}

var me = new Person('Lee');
console.log(me);

var you = Person('Kim');
console.log(you);
~~~

new 연산자와 함께 생성하 함수를 호출하면 this 바인딩이 메소드나 함수 호출 떄와는 다르게 동작합니다.



### 생성자 함수 동작 방식

new 연산자와 함께 생성자 함수를 호출하면 다음과 같은 수순으로 동작합니다.

1. 빈 객체 생성 및 this 바인딩

   생성자 함수의 코드가 실행되기 전 빈 객체가 생성되고, 이 빈 객체가 생성자 함수가 새로 생성하는 객체입니다. 이후 생성자 함수 내에서 사용되는 this는 이 빈 객체를 가리키며, 생성된 빈 객체는 생성자 함수의 prototype 프로퍼티가 가리키는 객체를 자신의 프로토타입 객체로 설정합니다.

2. this를 통한 프로퍼티 생성

   생성된 빈 객체에 this를 사용하여 동적으로 프로퍼티나 메소드를 생성할 수 있습니다. this는 새로 생성된 객체를 가리키므로 this를 통해 생성한 프로퍼티와 메소드는 새로 생성된 객체에 추가됩니다.

3. 생성된 객체 반환

   * 반환문이 없는 경우, this에 바인딩 된 새로 생성한 객체가 반환됩니다. 명시적으로 this를 반환하여도 결과는 같습니다.
   * 반환문이 this가 아닌 다른 객체를 명시적으로 반환하는 경우, this가 아닌 해당 객체가 반환됩니다. 이 때 this를 반환하지 않은 함수는 생성자 함수로서의 역할을 수행하지 못합니다. 따라서 생성자 함수는 반환문을 명시적으로 사용하지 않습니다. 



~~~
function Person(name) {
    // 생성자 함수 코드 실행 전	--- 1
    this.name = name; 	// ------ 2
    // 생성된 함수 반환  --------- 3
}
~~~



### 객체 리터럴 방식과 생성자 함수 방식의 차이

~~~javascript
// 객체 리터럴 방식
var foo = {
  name: 'foo',
  gender: 'male'
}

console.dir(foo);

// 생성자 함수 방식
function Person(name, gender) {
  this.name = name;
  this.gender = gender;
}

var me  = new Person('Lee', 'male');
console.dir(me);

var you = new Person('Kim', 'female');
console.dir(you);
~~~

객체 리터럴 방식과 생성자 함수 방식의 차이는 프로토타입 객체에 있습니다.

* 객체 리터럴의 경우, 생성된 객체의 프로토타입 객체는 Object.prototype
* 생성자 함수의 경우, 생성된 객체의 프로토타입 객체는 Person.prototype



### 생성자 함수에 new 연산자를 붙이지 않고 호출할 경우

일반 함수와 생성자 함수에 특별한 형식적 차이는 없고 new 연산자를 붙여서 호출하면 해당 함수는 생성자 함수로 동작합니다.

그러나 객체 생성 목적으로 작성된 생성자 함수를 new 없이 호출하거나, 반대로 일반 함수에 new를 붙여 호출하면 오류가 발생할 수 있습니다. 일반 함수와 생성자 함수의 호출 시 this 바인딩 방식이 다르기 때문입니다.

일반 함수를 호출하면 this는 전역 객체에 바인딩되지만, new 연산자와 함께 생성자 함수를 호출하면 this는 생성자 함수가 암묵적으로 생성된 빈 객체에 바인딩됩니다.

~~~javascript
function Person(name) {
  // new없이 호출하는 경우, 전역객체에 name 프로퍼티를 추가
  this.name = name;
};

// 일반 함수로서 호출되었기 때문에 객체를 암묵적으로 생성하여 반환하지 않는다.
// 일반 함수의 this는 전역객체를 가리킨다.
var me = Person('Lee');

console.log(me); // undefined
console.log(window.name); // Lee
~~~

생성자 함수를 new 없이 호출한 경우, 함수 Person 내부의 this는 전역객체를 가리키므로 name은 전역변우세 바인딩되며, 또한 new와 함께 생성자 함수를 호출하는 경우에 암묵적으로 반환하던 this도 반환하지 않으며, 반환문이 없으므로 undefined를 반환하게 됩니다.

일반 함수와 생성자 함수에 특별한 형식적 차이는 없기에 일반적으로 생성자 함수명은 첫 문자를 대문자로 기술하여 혼란을 방지하려는 노력을 합니다.

참고로 대부분의 빌트인 생성자(Object, Regex, Array 등)는 new 연산자와 함께 호출되었는지를 확인한 후 적절한 값을 반환합니다.

다시 말하지만 new 연산자와 함께 생성자 함수를 호출하는 경우, 생성자 함수 내부의  this는 생성자 함수에 의해 생성된 인스턴스를 가리킵니다. 따라서 아래 A 함수가 new 연산자와 함께 생성자 함수로써 호출되면 A 함수 내부의 this는 A 생성자 함수에 의해 생성된 인스턴스를 가리키는 것이구요.

~~~javascript
// Scope-Safe Constructor Pattern
function A(arg) {
  // 생성자 함수가 new 연산자와 함께 호출되면 함수의 선두에서 빈객체를 생성하고 this에 바인딩

  /*
  this가 호출된 함수(arguments.callee, 본 예제의 경우 A)의 인스턴스가 아니면 new 연산자를 사용하지 않은 것이므로 이 경우 new와 함께 생성자 함수를 호출하여 인스턴스를 반환
  arguments.callee는 호출된 함수의 이름을 나타냄. 이 예제의 경우 A로 표기하여도 문제없이 동작하지만 특정함수의 이름과 의존성을 없애기 위해서 arguments.callee를 사용하는 것이 좋음
  */
  if (!(this instanceof arguments.callee)) {
    return new arguments.callee(arg);
  }

  // 프로퍼티 생성과 값의 할당
  this.value = arg ? arg : 0;
}

var a = new A(100);
var b = A(10);

console.log(a.value);
console.log(b.value);
~~~

> callee는 arguments 객체의 프로퍼티로서 함수 바디 내에서 현재 실행 중인 함수를 참조할 때 사용합니다., 다시말해, 함수 바디 내에서 현재 실행 중인 함수의 이름을 반환합니다.



## 4. apply, call, bind 호출

