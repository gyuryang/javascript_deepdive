# 43장 Ajax

스터디 날짜: 2022년 11월 17일
완료 여부 : Done
작성자: 익명

## 43.1 Ajax란?

Ajax(Asynchronous JavaScript and XML) 

- 자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식
- 브라우저에서 제공하는 Web API 인 `XMLHttpRequest` 객체를 기반으로 동작한다.
- `XMLHttpRequest`는 HTTP 비동기 통신을 위한 메서드와 프로퍼티를 제공한다.
- `XMLHttpRequest`이 주목받기 이전의 웹페이지는 html태그로 시작해서 html태그로 끝나는 완전한 HTML을 서버로부터 전송받아 웹페이지 전체를 처음부터 다시 렌더링 하는 방식으로 작동했다.
- 즉 화면이 전활 될 떄마다 서버로부터 새로운 HTML을 전송받아 처음부터 다시 렌더링 해야하는 것이다.

- 과거의 단점
    - 변경이 필요없는 부분까지 다시 서버에 요청히여 불필요한 통신발생
    - 변경이 필요없는 부분까지 재렌더링하기때문에 화면 전환시 깜박이는 현상이 불가피
    - 서버와의 통신이 동기식으로 이루어지므로 응답이 있을 때까지 다음 동작을 블로킹
    
- Ajax의 도입
    - 변경이 필요한 부분만 비동기적으로 받아 부분만 렌더링

## 43.2 JSON

JSON(JavaScript Object Notation)

- 클라이언트와 서버 간의 HTTP 통신을 위한 테스트 데이터 포맷
- 자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷

### JSON 표기방식

- 자바스크립트의 객체 리터럴과 유사하게 키와 값으로 구성된 순수한 텍스트
    
    ```tsx
    {
    	"name" : "Lee",
    	"age" : 20,
    	"alive" : true,
    	"hobby" : ["traveling","tennis"]
    }
    ```
    
    - 키와 문자열은 반드시 큰따옴표로 묶어주어야함(작은 따옴표 불가)
    

### JSON.stringify

`JSON.stringify` 메서드는 객체를 JSON 포맷의 문자열로 변환한다. 클라이언트에서 서버로 데이터를 전송하려면 객체를 문자열화 해야하는데 이를 직렬화(serializing)이라고한다.

```tsx
const o = { name: 'Lee', gender: 'male', age: 20 };

// 객체 => JSON 형식의 문자열
const strObject = JSON.stringify(o);
console.log(typeof strObject, strObject);
// string {"name":"Lee","gender":"male","age":20}

// 객체 => JSON 형식의 문자열 + prettify
const strPrettyObject = JSON.stringify(o, null, 2);
console.log(typeof strPrettyObject, strPrettyObject);
/*
string {
  "name": "Lee",
  "gender": "male",
  "age": 20
}
*/

// replacer
// 값의 타입이 Number이면 필터링되어 반환되지 않는다.
function filter(key, value) {
  // undefined: 반환하지 않음
  return typeof value === 'number' ? undefined : value;
}

// 객체 => JSON 형식의 문자열 + replacer + prettify
const strFilteredObject = JSON.stringify(o, filter, 2);
console.log(typeof strFilteredObject, strFilteredObject);
/*
string {
  "name": "Lee",
  "gender": "male"
}
*/

const arr = [1, 5, 'false'];

// 배열 객체 => 문자열
const strArray = JSON.stringify(arr);
console.log(typeof strArray, strArray); // string [1,5,"false"]

// replacer
// 모든 값을 대문자로 변환된 문자열을 반환한다
function replaceToUpper(key, value) {
  return value.toString().toUpperCase();
}

// 배열 객체 => 문자열 + replacer
const strFilteredArray = JSON.stringify(arr, replaceToUpper);
console.log(typeof strFilteredArray, strFilteredArray); // string "1,5,FALSE"
```

### JSON.parse

`JSON.parse` 메서드는 JSON 포맷의 문자열을 객체로 변환한다. 서버에서 받은 JSON 문자열 데이터를 객체로 활용하기위해 사용하며, 역질렬화(deserializing)이라고한다.

```tsx
const o = { name: 'Lee', gender: 'male', age: 20 };

// 객체 => JSON 형식의 문자열
const strObject = JSON.stringify(o);
console.log(typeof strObject, strObject);
// string {"name":"Lee","gender":"male","age":20}

const arr = [1, 5, 'false'];

// 배열 객체 => 문자열
const strArray = JSON.stringify(arr);
console.log(typeof strArray, strArray); // string [1,5,"false"]

// JSON 형식의 문자열 => 객체
const obj = JSON.parse(strObject);
console.log(typeof obj, obj); // object { name: 'Lee', gender: 'male' }

// 문자열 => 배열 객체
const objArray = JSON.parse(strArray);
console.log(typeof objArray, objArray); // object [1, 5, "false"]
```

## 43.3 XMLHttpRequest

- 자바스크립트를 사용하여 HTTP요청을 전송하려면 `XMLHttpRequest` 객체를 사용한다.

### XMLHttpRequest 객체 생성

- `XMLHttpRequest` 객체는  `XMLHttpRequest` 생성자 함수를 호출하여 생성한다.
    
    ```tsx
    const xhr = new XMLHttpRequest();
    ```
    
- `XMLHttpRequest` 는 브라우저에서 제공하는Web API 이므로 브라우저에서만 정상 작동한다

### XMLHttpRequest 객체를 이용한 통신 예시

- **요청**

```tsx
// json으로 전송하는 경우
xhr.open('POST', '/users');

// 클라이언트가 서버로 전송할 데이터의 MIME-type 지정: json
xhr.setRequestHeader('Content-type', 'application/json');

const data = { id: 3, title: 'JavaScript', author: 'Park', price: 5000};

xhr.send(JSON.stringify(data));
```

- **응답 처리**
- 서버에서 언제 응답이 올지 모르므로,  `XMLHttpRequest`의 이벤트를 활용해 상태를 캐치하여 응답을 처리한다.

```tsx
// XMLHttpRequest 객체의 생성
const xhr = new XMLHttpRequest();

// 1. XMLHttpRequest.readyState 프로퍼티가 변경(이벤트 발생)될 때마다 onreadystatechange 이벤트 핸들러가 호출된다.
xhr.onreadystatechange = function (e) {
  // readyStates는 XMLHttpRequest의 상태(state)를 반환
  // readyState: 4 => DONE(서버 응답 완료)
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  // status는 response 상태 코드를 반환 : 200 => 정상 응답
  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log('Error!');
  }
};

// 2. load 이벤트는 서버 응답이 완료된 경우에 발생한다.
xhr.onload = function (e) {
  // status는 response 상태 코드를 반환 : 200 => 정상 응답
  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log('Error!');
  }
};
```

## ❗️axios & fetch

- Axios는 브라우저, Node.js를 위한 Promise API를 활용하는 HTTP 비동기 통신 라이브러리
    
    ```tsx
    export async function fetchEmailRegister(data: any) {
      let formData = new FormData();
      formData.append('representFile', data.representImage[0]);
      formData.append(
        'userAddForm',
        new Blob([JSON.stringify(data)], { type: 'application/json' })
      );
    
      const response = await publicApi.post(
        `/api/user/add?email=${sessionStorage.getItem('email')}`,
        formData,
        {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
        }
      );
      return response;
    }
    ```
    

- fetch : 자바스크립트 내장 라이브러리
    
    ```tsx
    export async function fetchSignUp(user) {
      return await (
        await fetch("/api/users/register", {
          method: "post",
          headers: {
            "content-type": "application/json",
          },
          body: JSON.stringify({
            username: user.username,
            email: user.email,
            password: user.password,
          }),
        })
      ).json();
    }
    ```
    

| Ajax | - 프로미스 기반 아님
- jquery와 호환성 좋음 |
| --- | --- |
| axios | - 프로미스 기반
- return이 프로미스이므로 response 처리 쉬움
- response timeout 존재 |
| fetch | - 프로미스 기반
- ES6 자바스크립트 내장 라이브러리
- response timeout 없음 : 서버 응답 계속 기다려야함
- JSON 변환 과정 필요 |