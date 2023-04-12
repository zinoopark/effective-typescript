# 타입이 값들의 집합이라고 생각하기

    - 타입스크립트의 타입의 종류
      - number : 숫자타입
      - string : 문자열 타입
      - boolean : true and false
      - void : 아무 값도 반환하지 않는 함수의 반환 타입
      - any : 모든 타입을 허용하는 타입
      - unknown : any와 유사하지만 더 엄격한 제한이 적용된 타입
      - null 과 undefined 값이 존재하지 않음을 나타내는 타입
      - object : 객체 타입
      - array : 배열타입
      - tuple : 고정된 요소 수와 타입을 가진 배열 타입 [string,number]
      - never : 절대 발생하지 않는 값을 나타내는 타입 -> 주로 예외 상황이나 무한 루프 등에서 사용
      - union : 두개 이상의 타입 중 하나를 나타내는 타입 -> string | number
      - intersection : 두 개 이상의 타입을 결합한 타입 -> person & serializable person과 serializable 타입의 프로퍼티를 모두 가지는 타입 41p 코드확인

    - 작은 집합은 한 가지 값만 포함하는 타입 -> 유닛 타입 또는 리터럴 타입 이라고 불림
      type A = 'A';
      type B = 'B';
      type Twelve = 12;
      문제!!!
      const Twelve = 12;
      let Twelve = 12;

# 상속 extends 42p 코드

# exclude

- exclude는 TypeScript에서 제공하는 유틸리티 타입 중 하나입니다. exclude는 두 개의 타입을 입력받아, 첫 번째 타입에서 두 번째 타입에 할당할 수 있는 타입을 제외한 나머지 타입들을 유니온 타입으로 반환함.
- 너무 어렵다요... -> 쉽게 설명하면 첫번째 값에서, 두번쨰 값을 뺀 값이 타입이된다.

```
type MyType = string | number | boolean;
type MyExcludeType = Exclude<MyType, string | boolean>;

```
위 코드에서 MyType는 string, number, boolean 타입 중 하나를 나타내는 유니온 타입입니다. MyExcludeType는 MyType에서 string과 boolean에 해당하는 타입을 제외한 number 타입을 가지게 됩니다. 
즉, MyExcludeType는 number 타입을 나타내는 타입입니다.

```
type MyObject = {
  name: string;
  age?: number;
}

type RequiredMyObject = {
  [K in keyof MyObject]-?: Exclude<MyObject[K], undefined>;
}

```
-? : 타입스크립트에서 제공하는 맵드 타입중 하나로 입력타입의 모든 프로퍼티를 필수 프로퍼티로 만드는 기능을 수행