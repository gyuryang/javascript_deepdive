# 15장 let, const 키워드와 블록 레벨 스코프

## 15.1 var 키워드로 선언한 변수의 문제점

### 15.1.1 변수 중복 선언 허용

- 초기화문이 있는 변수 선언문 —> var 키워드가 없는 것처럼 동작. 즉, 재할당 발생
- 초기화문이 없는 변수 선언문 —> 무시됨 (에러 발생 NO)

동일한 이름의 변수가 선언되어 있는 것을 모르고 변수를 중복선언 하면서 값까지 할당했다면 재할당으로 인해 의도치 않게 값이 변경되는 부작용 발생

### 15.1.2 함수 레벨 스코프

- var 키워드로 선언한 변수는 함수의 코드블록만을 스코프로 인정
- 즉, if문이나 for문의 코드블록에서 선언한 변수는 전역변수가 됨

### 15.1.3 변수 호이스팅

- var 키워드로 변수를 선언하면 변수 호이스팅에 의해 코드실행 이전에 변수 선언문이 미리 실행되어  변수 선언문 이전에 참조 가능

## 15.2 let 키워드

var 키워드의 단점을 보완하기 위해 새로 나온 변수 선언 키워드가 바로 ‘let’과 ‘const’다.

### 15.2.1 변수 중복 선언 금지

- ‘let’ 키워드로 변수를 선언하면 중복선언 시 에러가 발생하므로 ‘var’ 키워드로 변수를 선언했을 때처럼 중복선언으로 인한 재할당을 막을 수 있음

### 15.2.2 블록 레벨 스코프

- ‘let’ 키워드로 변수를 선언하면 함수 뿐 아니라 모든 코드 블록을 스코프로 취급하는 블록 레벨 스코프를 따름
- ‘var’ 키워드를 사용할 때보다 좀 더 전역변수의 남발을 줄일 수 있음

### 15.2.3 변수 호이스팅

- ‘let’ 키워드로 변수 선언 시, 선언 전에 변수를 참조하면 ReferenceError(참조에러)가 발생하므로 호이스팅이 발생하지 않는다고 생각할 수 있지만 실제로는 호이스팅 발생
    
    ```jsx
    let a= 1;
    {
    	console.log(a); // ReferenceError: Cannot access 'foo' before initialization
    	let a = 2;
    }
    ```
    

<aside>
🖥️ let 키워드로 선언한 a 변수가 호이스팅이 발생하지 않는다면 console.log는 전역변수 1을 참조해야 한다. 하지만 ReferenceError가 발생하고 있음. ReferenceError가 발생하는 이유는 a변수가 호이스팅 된 후 일시적 사각지대에 놓여져 있기 때문이다.

</aside>

- ‘let’ 키워드로 선언한 변수는 선언과 초기화가 분리되어 일어난다. 런타임 전 변수 선언문이 실행되어 변수가 선언되고 ‘undefined’로 초기화 되는 것이 아니라 초기화는 소스 코드 실행 후 변수 선언문에서 일어남
- 스코프 시작 지점부터 변수 선언문 이전까지의 변수를 참조할 수 없는 구간을 ‘일시적 사각지대’라 한다.

![                                   `let 선언`](15%E1%84%8C%E1%85%A1%E1%86%BC%20let,%20const%20%E1%84%8F%E1%85%B5%E1%84%8B%E1%85%AF%E1%84%83%E1%85%B3%E1%84%8B%E1%85%AA%20%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%A9%E1%86%A8%20%E1%84%85%E1%85%A6%E1%84%87%E1%85%A6%E1%86%AF%20%E1%84%89%E1%85%B3%E1%84%8F%E1%85%A9%E1%84%91%E1%85%B3%203e23cf1d8d384c96a972fc2a2a54f70e/let%25EC%2584%25A0%25EC%2596%25B8.png)

                                   `let 선언`

![var 선언.png](15%E1%84%8C%E1%85%A1%E1%86%BC%20let,%20const%20%E1%84%8F%E1%85%B5%E1%84%8B%E1%85%AF%E1%84%83%E1%85%B3%E1%84%8B%E1%85%AA%20%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%A9%E1%86%A8%20%E1%84%85%E1%85%A6%E1%84%87%E1%85%A6%E1%86%AF%20%E1%84%89%E1%85%B3%E1%84%8F%E1%85%A9%E1%84%91%E1%85%B3%203e23cf1d8d384c96a972fc2a2a54f70e/var_%25EC%2584%25A0%25EC%2596%25B8.png)

                           `var 선언`

- 자바스크립트는 var, let, const 뿐만 아니라 function, class 등 모든 선언문을 호이스팅 한다.

### 15.2.4 전역객체와 let

- let 키워드로 선언한 전역변수는 window 객체의 프로퍼티가 되지 않음
- let 키워드로 선언한 전역변수는 var 키워드로 선언한 변수와는 달리 렉시컬환경(스코프의 물리적 실체)의 선언적 환경 레코드(객체)에 따로 담긴다.

![렉시컬 환경 그림.jpg](15%E1%84%8C%E1%85%A1%E1%86%BC%20let,%20const%20%E1%84%8F%E1%85%B5%E1%84%8B%E1%85%AF%E1%84%83%E1%85%B3%E1%84%8B%E1%85%AA%20%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%A9%E1%86%A8%20%E1%84%85%E1%85%A6%E1%84%87%E1%85%A6%E1%86%AF%20%E1%84%89%E1%85%B3%E1%84%8F%E1%85%A9%E1%84%91%E1%85%B3%203e23cf1d8d384c96a972fc2a2a54f70e/%25EB%25A0%2589%25EC%258B%259C%25EC%25BB%25AC_%25ED%2599%2598%25EA%25B2%25BD_%25EA%25B7%25B8%25EB%25A6%25BC.jpg)

## 15.3 const 키워드

### 15.3.1 선언과 초기화

- const 키워드는 상수를 선언하기 위해 사용
- const 키워드로 선언한 변수는 반드시 선언과 동시에 초기화 해야함. 그렇지 않으면 Syntax Error 발생

### 15.3.2 재할당 금지

- const는 상수를 선언하는 것이므로 값이 변하면 안된다. 따라서 var, let과는 달리 const 키워드로 선언한 변수는 재할당이 금지된다.

### 15.3.3 상수

- 변수의 상대 개념인 상수는 재할당이 금지된 변수를 칭함

상수의 활용

```jsx
// 세율
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + (preTaxPrice * TAX_RATE);

console.log(afterTaxPrice) // 110
```

- 세율과 같이 쉽게 바뀌지 않고 고정된 값으로 쓰일 것 같은 값은 상수로 정의하면 상수이름으로 인해 의미파악이 쉬워 가독성이 올라감
- 또한, 프로그램 전체에서 공통적으로 사용하므로 세율이 변경되면 상수 하나만 변경하면 되 유지보수성도 올라감
- 상수는 스네이크 케이스 표기법을 사용

### 15.3.4 const 키워드와 객체

- 원시 값은 ‘불변의 값’ 즉, 변하지 못하는 값이므로 재할당으로만 변경이 가능한데, const 키워드로 선언한 변수는 재할당이 불가능하므로 한 번 할당한 원시값은 변경 X
- const 키워드는 재할당 금지할 뿐 ‘불변’을 의미하지 않음.
- const 변수에 객체를 할당한 경우 값 변경 가능. 객체는 재할당 없이 프로퍼티 등의 값을 변경 가능한 구조이기 때문
- 객체의 프로퍼티 값을 아무리 바꾸더라도 결국 객체를 가리키는 참조값은 그대로임

## 15.4 var vs let vs const

- var 키워드는 사용 X
- const 키워드는 재할당을 금지하여 var, let 키워드보다 안전하므로 기본적으로는 const 키워드 사용
- 재할당이 필요한 경우에 한해 let 키워드를 사용
