# 46장 제너레이터와 async/await

스터디 날짜: 2022년 11월 24일
완료 여부 : Done
작성자: 익명

## 46.1 제너레이터란?

1. 제너레이터 함수는 일반 함수와 달리 함수 호출자에게 함수 실행의 제어권을 양도할 수 있음
2. 제너레이터 함수는 함수 호출자에게 상태를 전달할 수 있고 함수 호출자로부터 상태를 전달 받을 수 있음. 즉, 양방향으로 상태를 주고 받을 수 있음
3. 제너레이터 함수를 호출하면 함수 코드를 실행하는 것이 아니라 이터러블이면서 이터레이터인 제너레이터 객체를 반환

## 46.2 제너레이터 함수의 정의

- 제너레이터 함수는 function* 키워드로 선언하고 하나 이상의 yield 표현식을 포함
    
    ```jsx
    // 제너레이터 함수 선언문
    function* getDecFunc(){
    	yield 1;
    }
    ```
    
- 애스터리크(*)는 function 키워드 바로 뒤에 붙이는 것을 권장
- 제너레이터 함수는 화살표함수로 정의할 수 없고, 생성자 함수로도 호출할 수 없음

## 46.3 제너레이터 객체

- 제너레이터 함수를 호출하면 반환하는 제너레이터 객체는 이터러블이면서 동시에 이터레이터임
- 제너레이터 객체는 next 메서드 뿐만 아니라 다른 이터레이터에는 없는 return, throw 메서드도 가지고 있음
    
    ```jsx
    function* genFunc() {
    	try {
    		yield 1;
    		yield 2;
    	} catch (e) {
    		console.log(e);
    	}
    }
    const generator = genFunc();
    
    console.log(generator.next()); // {value: 1, done: false}
    console.log(generator.return('End!')); // {value: "End!", done: true}
    console.log(generator.throw('Error!')); // {value: undefined, done: true}
    ```
    
    next() :  next 메서드를 호출하면 yield 표현식까지 코드블록 실행 후 이터레이터 리절트 객체를 반환하는데 이때 value 프로퍼티는 yield된 값을 프로퍼티 값으로 갖고 done 프로퍼티는 함수가 끝까지 실행되었는지 여부를 나타내는 불리언 값을 프로퍼티 값으로 가짐
    

## 46.4 제너레이터의 일시 중지와 재개

- 제너레이터 함수는 제너레이터 객체의 next 메서드를 통해 함수의 실행을 컨트롤 함
    
    ```jsx
    // 제너레이터 함수
    function* genFunc() {
    	yield 1;
    	yield 2;
    	yield 3;
    }
    // 제너레이터 객체 반환
    const generator = genFunc();
    
    console.log(generator.next()); // {value: 1, done: false}
    console.log(generator.next()); // {value: 2, done: false}
    console.log(generator.next()); // {value: 3, done: false}
    console.log(generator.next()); // {value: undefined, done: true}
    ```
    
    1. next 메서드의 첫 호출을 통해 함수의 실행을 시작하며 yield 표현식까지 실행되고 일시 중지되고 함수의 실행권이 next 메서드(호출자)로 양도됨
    2. next 메서드를 반복 호출하여 yield 표현식까지 실행과 중지를 반복
    3. 함수가 끝까지 실행되면 next 메서드가 반환하는 이터레이터 리절트 객체의 value 프로퍼티에는 제너레이터 함수의 반환값이 할당되고 done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었음을 나타내는 true가 할당됨
- 제너레이터 객체의 next 메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당됨
    
    ```jsx
    function* genFunc(){
    	const x = yield 1; // x = 10
    	const y = yield (x + 10); // y = 20
    	return x + y;
    }
    
    const generator = genFunc();
    
    // 처음 호출하는 next 메서드에 인수를 전달하면 무시됨
    let res = generator.next(10);
    console.log(res); // {value: 1, done: false}
    
    res = generator.next(10);
    console.log(res); // {value: 20, done: false}
    
    res = generator.next(20);
    console.log(res); // {value: 30, done: true} 
    ```
    

## 46.5 제너레이터의 활용

### 46.5.1 이터러블의 구현

- 제너레이터 함수를 사용하면 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단히 이터러블 구현 가능

### 46.5.2 비동기 처리

- 제너레이터를 활용하면 프로미스를 사용한 비동기 처리를 마치 동기 처리처럼 구현 가능
- 즉, 프로미스의 then / catch / finally 후속 처리 메서드 없이 비동기 처리 결과를 반환하도록 구현 가능

## 46.6 async / await

- ES8에서 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 async / await 도입

```jsx
const async = generatorFunc => {
	const generator = generatorFunc();

	const onResolved = arg => {
		const result = generator.next(arg);
		
		return result.done
			? result.value
			: result.value.then(res => onResolved);
	};
	
	return onResolved;
};

(async(function* fetchTodo() {
	const url = 'https://jsonplaceholder.typicode.com/todos/1'
	
	const response = yield fetch(url);
	const todo = yield response.json();
	console.log(todo);
	// {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
})());
```

- async / await를 사용하면 then / catch / finally 등의 후속 처리 메서드 사용 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현 할 수 있음
    
    ```jsx
    async function fetchTodo() {
    	const url = 'https://jsonplaceholder.typicode.com/todos/1';
    	
    	const response = await fetch(url);
    	const todo = await response.json();
    	console.log(todo);
    }
    
    fetchTodo();
    ```
    

### 46.6.1 async 함수

- await 키워드는 반드시 async 함수 안에 사용해야 함
- async 함수는 async 키워드를 사용해 정의하며 언제나 프로미스를 반환
    
    ```jsx
    async function foo(n) {return n;}
    foo(1).then(v => console.log(v)); // 1
    ```
    

### 46.6.2 await 키워드

- await 키워드는 프로미스가 비동기 처리가 수행(settled 상태)될 때까지 대기하다가 비동기 처리가 수행되면 프로미스의 처리 결과를 반환함
- await 키워드는 언제나 프로미스 앞에서 사용

### 46.6.3 에러 처리

- 비동기 처리를 위한 콜백 패턴의 단점 중 하나는 에러 처리가 곤란한 것
- 에러는 항상 호출자 방향으로 전파됨. 하지만 비동기 함수의 콜백 함수를 호출한 것은 비동기 함수가 아니기 때문에 try…catch 문에서 캐치할 수 없음
    
    ```jsx
    try {
    	setTimeout(() => { throw new Error('Error!');}, 1000);
    } catch (e) {
    	// 에러를 캐치하지 못함
    	console.error('캐치한 에러', e)
    }
    ```
    
- 콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확하여 try…catch문으로 에러를 캐치할 수 있음
    
    ```jsx
    const foo = async () => {
    	try {
    		const wrongUrl = 'https://wrong.url';
    		
    		const response = await fetch(wrongUrl);
    		const data = await response.json();
    		console.log(data);
    	} catch(e) {
    		console.error(err); // TypeError: Failed to fetch
    	}
    }
    ```