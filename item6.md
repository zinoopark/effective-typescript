# 타입스크립트를 설치하면 사용할 수 있는 두가지

- 타입스크립트 컴파일러(tsc)
- 단독으로 실행할 수 있는 타입스크립트 서버(tsserver)

# 타입스크립트의 목적

- 컴파일러를 실행하는것이 주된 목적

# 타입스크립트 서버

- 언어서비스 제공
  - 코드 자동 완성, 명세검사, 검색, 리팩터링을 포함

# 타입스크립트 언어서비스에서 제공하는것 -> 언어 서비스를 제공하도록 설정하는게 좋음

- 코드완성: 코드 작성 중에 자동으로 코드 조각을 완성해주는 기능
  - 변수, 함수, 클래스 등의 이름을 입력하다가 '.'을 입력하면 해당 객체의 프로퍼티나 메서드를 자동으로 추천
  - 타입체크: 타입스크립트는 정적 타입 언어이므로 코드를 작성할때 변수나 함수의 타입을 명시해야 하는데 이 때 코드를 분석하여 잘못된 타입 사용을 사전에 체크
  - 리팩토링: 코드를 수정하거나 개선할 떄, 변수나 함수 이름을 변경하거나 코드를 추출해 함수로 만드는 등의 작업을 수행하는 것을 언어서비스가 작업을 도와줌
  - 코드 포멧팅: 코드를 일관된 형식으로 정리해주는 기능 -> 들여쓰기, 줄 간격, 괄호등의 형식을 자동으로 조정
  - 코드 분석: 타입스크립트 언어서비스는 코드를 분석하여 코드의 구조와 흐름을 이해하며, 이를 바탕으로 코드 품질을 평가하고, 개선할 수 있는 지점을 제안

# 편집기

- 타입추론을 도와줌
- ex) let num = 10; 이라고 코드를 작성을 하고 마우스를 위에 올려두면 이 코드에 대한 타입 추론을 보여줌.

# 특정 시점에 타입스크립트가 값의 타입을 어떻게 이해하고 있는지 살펴보는것은 '타입 넓히기', '타입 좁히기'

    let x = 3;

- 타입 넓히기 (Type Widening): 변수에 값을 할당할 때, 해당 값의 타입을 기준으로 변수의 타입이 자동으로 확장이됨. 예를 들어, 다음과 같은 코드를 작성하면
  변수 x의 타입은 자동으로 number로 넓혀짐. 이후에 x 변수에 다른 타입의 값을 할당하면, 컴파일러가 오류를 발생

```
  function printValue(value: string | number) {
    if (typeof value === "string") {
      console.log(value.toUpperCase());
    } else {
      console.log(value.toFixed(2));
    }
  }
```
- 타입 좁히기 (Type Narrowing): 변수의 타입을 한정하는 것으로, 변수가 가질 수 있는 값의 범위를 좁힙니다. 예를 들어, 다음과 같은 코드를 작성하면
  value 변수의 타입이 string 또는 number로 지정이 됩니다. 이후에 value 변수를 사용할 때, typeof 연산자를 사용하여 string인지 number인지 체크하고,
  string일 경우 toUpperCase() 메서드를 호출하고, number일 경우 toFixed() 메서드를 호출합니다. 이를 통해 value 변수의 타입을 한정하고, 타입 체크를 보다 엄격하게 수행할 수 있음.


# 타입스크립트 언어서비스는 편집기상에 'GO to Definition' 기능을 제공
ch02 - 06 - 02.ts 