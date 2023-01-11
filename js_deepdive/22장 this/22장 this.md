# 22장 this

스터디 날짜: 2022년 10월 6일
완료 여부 : Done
작성자: 익명

## 22.1 this 키워드

- 메서드는 자신이 속한 객체의 상태, 즉 프로퍼티를 참조하고 변경할 수 있어야한다. 이때 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 우선 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야한다.
- 객체 리터럴 방식으로 생성한 객체의 경우 메서드 내부에서  메서드 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조할 수 있다.
    - circle에 할당되기 전에 이미 객체 리터럴 평가가 완료되어 객체가 생성되었기 때문에, getDiameter가 호출되는 시점에는 이미 circle에 객체가 할당된 이후이다. 따라서 메서드 내부에서 circle식별자를 참조할 수 있는 것이다.
    
    ```tsx
    const circle = {
    	radius: 5,
    	getDiameter(){
    		return 2* circle.radius;	
    	}
    };
    
    console.log(circle.getDiameter()); //10
    ```
    

- 그러나 생성자함수 방식으로 인스턴스를 생성하는 경우를 생각해보자
    - 생성자함수는 생성자함수를 정의한 이후, new연산자와 함께 생성자 함수를 호출하는 단계가 필요하다. 즉, 생성자함수로 인스턴스를 생성하려면 먼저 생성자 함수가 존재해야한다.
    - 생성자함수를 정의하는 시점에는 아직 인스턴스를 생성하기 전이므로, 생성자함수가 생성할 인스터스를 가리키는 식별자가 무엇인지 아직 알수없다.
    
    ```tsx
    function Circle(radius) {
    	???.radius = radius;
    };
    
    Circle.prototype.getDiameter = function () {
    	return 2 * ???.radius;
    };
    
    const circle = new Circle(5);
    ```
    
    - 이럴 때 자신이 속한 객체 혹은 자신이 생성할 인스턴스를 가리키는 특수한 식별자가 필요했고, 그것이 바로 this이다.
    - this는 자신이 속한 객체 혹은 자신이 생성할 인스턴스를 가리키는 자기참조 변수이다.
    - this는 arguments와 마찬가지로 함수를 호출할 때 암묵적으로 함수내부에 전달되어, 지역변수처럼 사용할 수 있다.
    - 단, this가 가리키는 값, 즉 바인딩은 함수 호출 방식에 의해 동적으로 결정된다.

- this는 전역에서, 지역에서 모두 참조할 수 있다. 전역과 일반함수에서 this는 window를 가리킨다.
- this는 객체의 프로퍼티나 메서드를 참조하기 위한 자기참조 변수이므로 일반적으로 객체의 메서드 내부나 생성자함수 내부가 아니면 의미가 없다.  때문에 strict mode 의 일반 함수에서 this에 undefined가 바인딩 되는 것이다.
- 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다.
- 생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다.

## 22.2 함수 호출 방식과 this 바인딩

this에 바인딩 될 값은 함수호출방식에 따라 동적으로 결정된다.

❗️ 함수의 상위 스코프를 결정하는 방식인 렉시컬 스코프(Lexical scope)는 함수를 선언할 때 결정된다. this 바인딩은 함수 호출 시점에 결정된다.

## 함수호출 방식

```tsx
var foo = function () {
  console.dir(this);
};

// 1. 함수 호출
foo(); // window
// window.foo();

// 2. 메소드 호출
var obj = { foo: foo };
obj.foo(); // obj

// 3. 생성자 함수 호출
var instance = new foo(); // instance

// 4. apply/call/bind 호출
var bar = { name: 'bar' };
foo.call(bar);   // bar
foo.apply(bar);  // bar
foo.bind(bar)(); // bar
```

### 일반 함수 호출

1. 일반함수호출에서 this는 전역객체인 window를 가리킨다.
2. 메서드 내에서 정의한 중첩함수 또한 일반함수로 호출 시  this가 전역 객체로 바인딩된다.
3. 콜백함수 또한 일반함수로 호출된다면 this는 전역 객체로 바인딩된다.

⇒ 즉 일반한수로 호출된 모든 함수(중첩함수,콜백함수 등등) 내부의 this에는 전역객체가 바인딩된다. 하지만 헬퍼 함수 역할인 메서드 내에서 정의한 중첩함수 혹은 메서드로 전달되는 콜백함수의 this가 window로 바인딩 되는 것은 문제가 있다.

```tsx
var value = 1;

var obj = {
  value: 100,
  foo: function() {

    var that = this;  // 1) that에 this 할당해서 사용하여 위의 문제 회피

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

// 2) 위 방법 이외에도 자바스크립트는 this를 명시적으로 바인딩할 수 있는 
// apply, call, bind 메소드를 제공한다.

// 3) 화살표 함수를 이용할 수도 있다.

const obj2 ={
	value : 100,
	foo() {
	setTimeout(()=>{console.log(this.value), 100) //100
}
```

### 메서드 호출

1. 메서드 내부의 this에는 메서드를 호출한 객체가 바인딩된다.
2. 메서드 내부의 this는 메서드를 소유한 객체가 아닌 메서드를 호출한 객체를 바인딩한다.
3. 메서드는 다른 객체의 메서드가 될수도 있으며, 일반 변수에 할당하여 일반 함수로 호출할 수도있다. 따라서 메서드에 속한 this는 자신을 호출한 객체를 가리킨다.

### 생성자 함수 호출

- 생성자 함수 내부의 this에는 생성자함수가 (미래에)생성할 인스턴스가 바인딩된다.
    
    **1. 빈 객체 생성 및 this 바인딩**
    
    생성자 함수의 코드가 실행되기 전 빈 객체가 생성된다. 이 빈 객체가 생성자 함수가 새로 생성하는 객체이다. 이후 **생성자 함수 내에서 사용되는 this는 이 빈 객체를 가리킨다.** 그리고 생성된 빈 객체는 생성자 함수의 prototype 프로퍼티가 가리키는 객체를 자신의 프로토타입 객체로 설정한다.
    
    **2. this를 통한 프로퍼티 생성**
    
    생성된 빈 객체에 this를 사용하여 동적으로 프로퍼티나 메소드를 생성할 수 있다. this는 새로 생성된 객체를 가리키므로 this를 통해 생성한 프로퍼티와 메소드는 새로 생성된 객체에 추가된다.
    
    **3. 생성된 객체 반환**
    
    • 반환문이 없는 경우, this에 바인딩된 새로 생성한 객체가 반환된다. 명시적으로 this를 반환하여도 결과는 같다.
    • 반환문이 this가 아닌 다른 객체를 명시적으로 반환하는 경우, this가 아닌 해당 객체가 반환된다. 이때 this를 반환하지 않은 함수는 생성자 함수로서의 역할을 수행하지 못한다. 따라서 생성자 함수는 반환문을 명시적으로 사용하지 않는다.
    

### apply/call/bind 호출

1. apply, call, bind 메서드는 Function.prototype의 메서드이다. 즉, 이들 메서드는 모든 함수가 상속받아 사용할 수 있다.
2. this에 바인딩될 객체는 함수 호출 패턴에 의해 결정된다. apply/call/bind는  이러한 자바스크립트 엔진의 암묵적 this 바인딩 이외에 this를 특정 객체에 명시적으로 바인딩하는 방법이다.

`apply()` && `call()` :  메소드를 호출하는 주체는 함수이며 apply()와  call() 메소드는 this를 특정 객체에 바인딩할 뿐 본질적인 기능은 함수 호출이다.

```tsx
Function.prototype.apply(thisArg, [argsArray])
// thisArg: 함수 내부의 this에 바인딩할 객체
// argsArray: 함수에 전달할 argument의 배열

Function.prototype.call(thisArg, arg1, arg2, arg3, ...)
// thisArg: 함수 내부의 this에 바인딩할 객체
// argsArray: 함수에 전달할 argument들을 리스트형태로 전달함
```

- apply() 와 call() 의 대표적인 용도는 argument와 갑은 유사 배열 객체에 배열 메서드를 사용하는 경우이다. 가령 Array.prototype.slice 같은 배열의 메서드를 사용할 수는 없으나 apply와 call메서드를 사용하면 가능하다.

```tsx
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // slice: 배열의 특정 부분에 대한 복사본을 생성한다.
  var arr = Array.prototype.slice.apply(arguments); // arguments.slice
  // var arr = [].slice.apply(arguments);

  console.log(arr);
  return arr;
}

convertArgsToArray(1, 2, 3);
```

`bind()` 는 apply나 call 처럼 함수를 호출하지 않는다. 다만 첫번째 인수로 전달할 값으로 this바인딩이 교체된 함수를 새롭게 생성해 반환한다. 함수를 호출하지 않으므로, 명시적으로 호출하여 사용해야한다.

```tsx
function getThisBinding() {
	return this;
}

const thisArg ={ a : 1 };

console.log(getThisBinding.bind(thisArg)); //getThisBinding()

console.log(getThisBinding.bind(thisArg)()); //{ a : 1}

```

- 바인드는 메소드 내부의 중첩함수가 의도와달리 전역객체에 바인딩됐을 때 유용하게 사용할 수 있다.