# 37장 Set과 Map

스터디 날짜: 2022년 11월 10일
완료 여부 : Done
작성자: 익명

## 37.1 Set

- 중복되지 않는 유일한 값들의 집합
- Set 객체는 이터러블임

### Set 객체의 생성

- Set 생성자 함수로 생성
- 인수로 전달받은 이터러블에서 중복된 값은 제외하고 Set 객체를 생성
    
    ```tsx
    const set = new Set();
    const set1 = new Set([1,2,1]); // Set(2) {1,2}
    const set2 = new Set('banana'); // Set(3) {'b','a','n'}
    ```
    

- set과 배열을 적절히 사용하면 중복제거에 용이하다.
    
    ```tsx
    const unique = arr => [...new Set(arr)];
    console.log(unique([1,2,1])); // [1,2]
    ```
    

### 요소 개수 확인

- `Set.prototype.size` 를 이용해 확인할 수 있음 (length와 유사)
- length와 유사하지만, size 프로퍼티는 setter 함수가 없기 때문에 할당해도 변하지 않는다.
    
    ```tsx
    const set = new Set([1,2,1]);
    console.log(set.size); // 2
    set.size = 100;
    console.log(set.size); // 2
    ```
    

### 요소 추가

- `Set.prototype.add` 로 추가할 수 있다. (push와 유사)
- Set객체에 요소를 추가한 새로운 객체를 반환. 중복된 값은 추가되지 않고 에러발생하지 않음
    
    ```tsx
    const set = new Set();
    set.add(1).add(2).add(3);
    console.log(set); // Set(3) {1,2,3}
    set.add(1).add(1).add(1);
    console.log(set); // Set(3) {1,2,3}
    ```
    
- 0과  -0, NaN과 NaN는 같다고 보고 중복추가하지 않음
- 자바스크립트의 모든 값은 Set에 저장할 수 있음

### 요소 존재 여부 확인

- `Set.prototype.has` 메서드로 확인할 수 있다. (includes와 유사)
    
    ```tsx
    const set = new Set([1,2]);
    set.has(1); // true
    set.has(4); // false
    ```
    

### 요소 삭제

- `Set.prototype.delete` 메서드를 이용해 삭제할 수 있다.
- 인수로 삭제할 요소를 전달하면 반환값은 성공 여부를 boolean으로 반환함
- 없는 값을 삭제하면 에러가 아닌 false 반환
    
    ```tsx
    const set = new Set([1,2,3]);
    set.delete(1); // true
    set.delete(5); // false
    console.log(set); // Set(2) {2,3}
    ```
    

### 요소 일괄 삭제

- `Set.prototype.clear` 로 초기화
- 반환값은 undefined
    
    ```tsx
    const set = new Set([1,2,3,4,5]);
    set.clear(); // undefined
    console.log(set); // Set(0) {}
    ```
    

### 요소 순회

- `Set.prototype.forEach`메서드로 요소순회
- `Array.prototype.forEach((item,index,self)⇒{})` 와 같지만, Set의 경우 index가 없으므로, 콜백함수의 두번째 인자가 index가 아니고 첫번쨰 인자와 동일하게 item임을 유의
- Set 객체는 이터러블이므로, for…of, 스프레드, 디스트럭처링 할당이 가능하다
- index가 없지만 다른 이터러블과의 호환성 유지를 위해 추가된 순서대로 순회한다.

### 집합 연산

- 교집합
    
    ```tsx
    Set.prototype.교집합 = function(otherSet){
    	return new Set([...this].filter(x=>otherSet.has(x)));
    }
    ```
    

- 합집합
    
    ```tsx
    Set.prototype.합집합 = function(otherSet){
    	return new Set([...this,...otherSet]);
    }
    ```
    

- 차집합
    
    ```tsx
    Set.prototype.차집합 = function(otherSet){
        return new Set([...this].filter(x=> !otherSet.has(x)));
    }
    ```
    

- 부분집합
    
    ```tsx
    Set.prototype.부분집합 = function(otherSet){
        for (const item of this){
            if (!otherSet.has(item)) return false;
        }
        return true;
    }
    ```
    

## 37.2 Map

- 키와 값의 쌍으로 이루어진 컬렉션
- 객체와 유사하지만 객체는 키로 문자열,심벌 값만 가능한데 Map 객체는 키로 모든 값이 가능함
- Map 객체는 이터러블임

### Map 객체의 생성

- Map 생성자 함수로 생성
- 키와 값의 쌍으로 이루어진 요소로 구성된 이터러블을 인수로 전달받는다
- 중복된 키를 갖는 요소가 있으면 덮어 쓴다
    
    ```tsx
    const map = new Map();
    const map2 = new Map([['key1','value1'],['key2','value2']]);
    
    const map3 = new Map([['key1','value1'],['key1','value2']]);
    console.log(map3); // Map(1) {'key1' => 'value2'}
    ```
    

### 요소 개수 확인

- `Map.prototype.size`로 확인한다.
- setter없이 getter만 있는 접근자 프로퍼티로, Set.size와 마찬가지로 할당해도 변화 없음
    
    ```tsx
    const map = new Map([['key1','value1'],['key2','value2']]);
    console.log(map.size); // 2
    map.size = 100;
    console.log(map.size); // 2
    ```
    

### 요소 추가

- `Map.prototype.set` 메서드로 추가
- Set.add와 유사하게 새로운 Map 객체를 반환하기 때문에 .set().set() 가능함
- 키값으로 -0과0, NaN과 NaN은 같기때문에 덮어씌움
    
    ```tsx
    const map = new Map();
    map.set('key1','value1');
    ```
    

- 키값으로 모든 값을 사용할 수 있다.
    
    ```tsx
    const map = new Map();
    const p1 = {name:'aj'};
    const p2 = {name:'mj'};
    map.set(p1,'male');
    map.set(p2,'female');
    ```
    

### 요소 취득

- `Map.prototype.get` 메소드로 취득
- 인수로 키 값을 전달, 해당 키를 갖는 요소가 없을 시 undefined를 반환
    
    ```tsx
    const map = new Map([['key1','value1'],['key2','value2']]);
    map.get('key1'); // 'value1'
    ```
    

### 요소 존재 여부 확인

- `Map.prototype.has` 메서드로 확인
- 인수로 키 값을 전달하고, 반환값은 있으면 true, 없으면 false 반환

### 요소 삭제

- `Map.prototype.delete` 메서드로 삭제
- 인수로 전달받은 값을 키로 갖는 요소를 삭제
- 반환값은 성공시 true, 실패시 false(에러는 안남)

### 요소 일괄 삭제

- `Map.prototype.clear` 로 모두 삭제할 수 있음
- 반환값은 undefined

### 요소 순회

- `Map.prototype.forEach` 메서드로 순회
- 콜백 함수 내부의 3가지 인수는 요소값,요소의 키, Map객체 자체
- Map 객체는 이터러블이기 때문에 for...of, 스프레드 문법, 디스트럭처링 할당 가능

- 이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서드
    - `Map.prototype.keys`
        
        ```tsx
        const myMap = new Map();
        myMap.set("0", "foo");
        myMap.set(1, "bar");
        myMap.set({}, "baz");
        
        const mapIter = myMap.keys();
        
        console.log(mapIter); // MapIterator {'0', 1, {…}}
        console.log(mapIter.next().value); // "0"
        console.log(mapIter.next().value); // 1
        console.log(mapIter.next().value); // {}
        ```
        
    
    - `Map.prototype.values`
        
        ```tsx
        const myMap = new Map();
        myMap.set('0', 'foo');
        myMap.set(1, 'bar');
        myMap.set({}, 'baz');
        
        const mapIter = myMap.values();
        
        console.log(mapIter); // MapIterator {'foo', 'bar', 'baz'}
        console.log(mapIter.next().value); // "foo"
        console.log(mapIter.next().value); // "bar"
        console.log(mapIter.next().value); // "baz"
        ```
        
    
    - `Map.prototype.entries`
        
        ```tsx
        const myMap = new Map();
        myMap.set('0', 'foo');
        myMap.set(1, 'bar');
        myMap.set({}, 'baz');
        
        const mapIter = myMap.entries();
        
        console.log(mapIter); // MapIterator {'0' => 'foo', 1 => 'bar', {…} => 'baz'}
        console.log(mapIter.next().value); // ["0", "foo"]
        console.log(mapIter.next().value); // [1, "bar"]
        console.log(mapIter.next().value); // [Object, "baz"]
        ```