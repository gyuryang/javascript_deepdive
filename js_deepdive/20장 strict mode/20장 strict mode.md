# 20장 strict mode

스터디 날짜: 2022년 10월 6일
완료 여부 : Done
작성자: 익명

## strict mode

```tsx
function foo() {
	x=10;
}

foo();

console.log(x);
```

- x를 선언하지 않고 값을 할당했을 때 함수 안에 x가 없으므로, 전역 스코프로 넓혀 x를 탐색한다.
- 하지만 전역에도 x가 없으므로, error가 발생할 것 같지만 암묵적으로 전역 객체에 x프로퍼티를 동적 생성한다.
- 이때 x프로퍼티는 마치 전역 변수처럼 사용할 수 있다. 이러한 현상을 암묵적 전역이라고 한다.
- 개발자의 의도와는 상관없이 발생한 암묵적 전역은 에러를 야기할 수 있으므로, let const 키워드를 사용하여 변수를 선언한 다음 사용해야한다.

- 암묵적 전역이나 오타, 문법지식 미비로 인한 실수를 방지하기 위하여 ES5 부터는 strict mode가 추가되었다. strict mode는 자바스크립트 언어의 문법을 더 엄격하게 적용한 것으로, 잠재적 오류를 방지하기 위한 명시적 에러를 발생시킨다.
- ESLint와 같은 도구를 이용해도 비슷한 효과를 낼 수 있다.

### 주의사항

- strict mode를 적용하려면 전역 선두, 혹은 함수 몸체 선두에 `use strict`를 추가한다.
- strict mode는 script 단위로 적용되므로, 전역에 strict mode를 적용하는 것을 피하라. strict 과  non-strict를 혼용하는 것은 좋지 않다 (외부라이브러리가 non-strict인 경우도 있으므로)
- 함수단위로 strict mode를 적용하는 것을 피해라.

### strict mode가 발생시키는 에러

- 암묵적 전역: 선언하지 않는 변수를 참조하면 `ReferenceError`
- 변수, 함수, 매개변수의 삭제:  delete로 변수, 함수, 매개변수를 삭제하면 `SyntaxError`
- 매개변수 이름의 중복: 중복된 매개변수 명 사용 시  `SyntaxError`
- with문의 사용: with 문을 사용하면 `SyntaxError`

### strict mode 적용에 의한 변화

- 일반함수의 this : 함수를 일반함수로서  호출하면 this에 undefined가 바인딩된다. 생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다. 이때 에러는 발생하지 않는다

```tsx
(function () {
  'use strict';

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
}());
```

❗️생성자 함수  :  이름은 대문자로 시작해야하고, 호출 시 new 연산자와 함께 사용한다. 

<생성자 함수 작동 알고리즘>

1. 빈 객체를 만들어 `this`에 할당합니다.
2. 함수 본문을 실행합니다. `this`에 새로운 프로퍼티를 추가해 `this`를 수정합니다.
3. `this`를 반환합니다.

- arguments 객체 : strict mode에서는  매개변수에 전달된 인수를 재할당하여 변경해도 argumemts 객체에 반영되지 않는다.

```tsx
(function (a) {
	'use strict';
	a = 2;
	console.log(arguments); //{0: 1, length:1}
}(1)
)
```