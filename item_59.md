# 타입스크립트 도입 전에 @ts-check 와 JSDoc 으로 시험해보기

> 한줄요약 : 마이그레이션시 @ts-check -> noImplicitAny -> ... 처럼 단계적으로 타입체크 강도를 올린다.

## @ts-check 사용예시
```javascript
//@ts-check
const person = {first: 'Grace', last: 'Mapper'};

2 * person.first
// ~~~~~~~~~~~~~ 산술 연산 오른쪽은 'any', 'number', 'bigint'
//               또는 열거형 형식이여야 합니다.
```

@ts-check 지시자가 찾아낼 수 있는 오류.

1. 타입 불일치

2. 매개변수 개수 불일치

3. 선언되지 않은 전역변수

```javascript
    //@ts-check
    console.log(user.firstName);
            // ~~~~~ 'user' 이름을 찾을 수 없습니다.
```
해결법 : 타입선언! -> `type.d.ts` 파일에 user 선언

```javascript
    interface UserData {
        firstName: string,
        lastName: string;
    }
    declare let user: UserData;
```

트리플 슬래시 참조를 활용한 명시적 임포트

```javascript
    //@ts-check
   /// <reference path="./types.d.ts" />
   console.log(user.firstName); // 정상
```


4. 알 수 없는 라이브러리

> 제이쿼리 `$` 사용시 @ts-check 지시자를 사용하면 오류가 발생한다.

해결법 : 제이쿼리 타입선언 라이브러리 설치.

5. DOM 문제

```javascript
    // @ts-check
    const ageEl = document.getElementById('age');
    ageEl.value = '12';
    //~~~~~  'HTMLElement' 유형에 'value' 속성이 없습니다.
```
HTMLInputElement 타입에는 value 속성이 있지만, 더 상위 개념인 HTMLElement 타입을 반환하는 것이 오류.
js 이기 때문에 타입 단언 사용 불가.

해결법 : JSDoc 사용
```javascript
    // @ts-check
    const ageEl = /** @type {HTMLInputElement} */ (document.getElementById('age'));
    ageEl.value = '12'; // 정상
```



그러나 , JSDoc이 이미 프로젝트에 사용중인 경우 기존 주석에 타입 체커가 동작해 수많은 오류가 발생 할 수 있다.
@ts-check를 사용하는 이유는 어디까지나 ts로의 마이그레이션을 돕기 위한 중간단계이므로, 너무 많은 공수가 들어간다면 비추!


