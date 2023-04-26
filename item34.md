# 부정확한 타입보다는 미완성 타입을 사용하기
 > 두줄요약 
 1. 타입이 잘못된것보다 없는게 낫다. (테스트로 커버하라)
 2. 타입을 잘 모를때는 any와 unknown을 구분해서 사용하자

 `any` : 어떠한 값이든 가능. 타입을 좁혀서 사용하지 않아도 된다.
 `unknown` : 어떠한 값이 올 수 있는지 모를 때 타입을 좁혀서 사용한다. any타입 이외의 다른 값에 할당이 불가능하다.


ex1) GeoJSON
```javascript
interface Point {
  type: 'Point';
  coordinates: number[];
}
```
coordinates는 위도,경도만 들어가므로
```javascript
interface Point {
  type: 'Point';
  coordinates: [number, number];
}
```
더 정확한 타입으로 바꿔보자.
그러나 고도가 추가될 수도 있고, 다른 추가적인 정보가 들어갈 수 도있다는 점에서 오류가 발생할 수 있다.
즉 타입이 오히려 부정확해 졌다.

ex2) Mapbox 라이브러리

1. 모두허용
2. 문자열, 숫자, 배열 허용
3. 문자열, 숫자, 알려진 함수 이름으로 시작하는 배열 허용
4. 각 함수가 받는 매개변수의 개수가 정확한지 확인
5. 각 함수가 받는 매개변수의 타입이 정확한지 확인

```javascript
type Expression1 = any;
type Expression2 = number | string | any[];

const tests: Expressions3[] = [
  10,
  'red',
  true, //error true is not in Expression2
  ['+', 10, 5],
  ['case', ['>', 20, 10], 'red', 'blue', 'green'], // 값이 많다는 오류가 발생해야한다.
  ['**', 2, 31], // 오류가 발생해야 하는데 발생하지 않음
  ['rgb', 255, 123, 64], // 값이 많다는 오류가 발생해야한다.
];
```

정밀도를 올리기위해, 튜플의 첫번째요소를 리터럴 유니온으로 바꿔보자!
```javascript
type FnName = '+' | '-' | '*' | '/' | '<' | '>' | 'case' | 'rgb';
type CallExpression = [FnName, ...any[]];
type Expression3 = number | string | CallExpression;

const tests: Expressions3[] = [
  10,
  'red',
  true, //error true is not in Expression3
  ['+', 10, 5],
  ['case', ['>', 20, 10], 'red', 'blue', 'green'],
  ['**', 2, 31], //error "**" is not in FnName
  ['rgb', 255, 123, 64],
];
```

에러를 하나더 뱉도록 정밀도를 높였다.
rgb, case, 수학연산에 대한 타입을 좀더 정밀하게 나눠보자.
```javascript
type Expression4 = number | string | CallExpression;
type CallExpression = MathCall | caseCall | RGBCall;

interface MathCall {
  0: '+' | '-' | '*' |'/' |'<'| '>';
  1: Expression4;
  2: Expression4;
  length:3
}

interface CaseCall {
  0: 'case';
  1: Expression4;
  2: Expression4;
  3: Expression4;
  length: 4 | 6 | 8 | 10 | 12 | 16 // 등등
}

interface RGBCall {
  0: 'RGB';
  1: Expression4;
  2: Expression4;
  3: Expression4;
  length: 4;
}

const tests: Expression4[] = [
    10,
  "red",
  true, //error true is not in Expression4
  ["+", 10, 5],
  ["case", [">", 20, 10], "red", "blue", "green"],
  //["case", [">", ...],...] is not in 'string'
  ["**", 2, 31], //error 'number' is not in 'string'
  ["rgb",255,123,64],
  ["rgb",255,123,64, 74], //error 'number' is not in 'string'
]
```
에러를 파악하기가 오히려 더 어렵다. 
**에 대한 에러는 더 부정확해졌고, 길이가 잘못되었다는 에러가 아닌 엉뚱한 에러를 뱉었다.
결과 파악이 오히려 어려워졌다.

결론: 정밀도도 과유불급





