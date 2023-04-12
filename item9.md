# 타입 단언보다는 타입 선언을 사용하기
```
interface Person {name: string};

const alice: Person = {name: "Alice}; // 타입은 Person   -> 타입선언을 명시
const bob = {name: 'Bob'} as Person;  -> as Person 은 Person으로 단언 하겠다. 난 Person으로 보겠음..!!!
```

- 타입 단언 보다는 타입 선언을 사용하는게 좋다.

단 dom 에 직접 접근 하는 경우에는 타입 단언을 해주어야 한다. 타입스크립트는 DOM에 접근할 수 없기 떄문입니다.
```
const el = document.getElementById('foo')!;
```