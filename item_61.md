# 의존성 관계에 따라 모듈 단위로 전환하기

마이그레이션시 전환 순서

> 두줄요약 : 
> 마이그레이션시 전환 순서
> 1. 서드파티 모듈과 외부 API호출에 대한 @types를 추가하라.
> 2. 모듈 단위로 전환하되 의존성 트리에서 아래부터 위로 ( 리팩토링 x, 타입만 지정한다. )

- madge라는 의존성트리를 그려주는 라이브러리 활용하라

마이그레이션시 발생 할 수 있는 오류

1. 선언되지 않은 클래스 멤버.

```javascript
    class Greeting {
        constructor(name) {
            this.greeting = 'Hello';
            //  ~~~~~~~~~   Greeting 유형에 greeting 속성이 없습니다.
            this.name = name;
            //   ~~~~   Greeting 유형에 name 속성이 없습니다.
        }
        greet() {
            return this.greeting + ' ' + this.name;
                //     ~~~~~~~~~~            ~~~~~~   속성이 없습니다.
        }
    }

```

해결법 : 빠른 수정 ㅎ_ㅎ -> 누락된 모든 멤버 추가. -> any로 추론된 부분 직접 수정

2. 타입이 바뀌는 값

```javascript
 const state = {};
 state.name = 'New York';
    //~~~~~~ '{}' 유형에 'name' 속성이 없습니다.
 state.capital = 'Albany';
     //~~~~~~~ '{}' 유형에 'capital' 속성이 없습니다.
```

해결법:
1. 한번에 생성
```javascript
 const state = {
    name = 'New York';
    capital = 'Albany';
 };

```

2. 타입 단언
```javascript
interface State = {
   name = 'New York';
   capital = 'Albany';
};

const state = {} as State;
state.name = 'New York';
state.capital = 'Albany';
```


3. @ts-check로 에러를 체크하고 있었다면, js-> ts로 바꾸는 순간 문제가된다.

해결법: JSDoc 타입정보가 있다면 빠른수정 기능이 있다. 그 이후 JSDoc을 제거하라.



