# 객체 래퍼 타입 피하기
자바스크립트 기본형 값 (string,number,boolean,null,undefined,symbol,bigint) 총7개
 - 기본형들은 불변(immutable)한 값을 가지며 메서드를 가지지 않는다는 점에서 객체와 구분이 됨.
 - string 인 경우는 메서드를 가지고 있는 것처럼 보인다. 아례의 예제를 봅시다.
node
'primitive'.charAt(3)
charAt은 메서드가 x
- string 기본형에는 메서드가 없지만 자바스크립트 내부에 메서드를 가지는 String 객체 타입이 정의 되어 있다.
자바스크립트 내부 String 객체타입 :  charAt(pos: number): string;
자바스크립트에서는 기본형과 객체 타입을 서로 자유롭게 변환이 가능한데 string 기본형에 charAt 같은 메서드를 사용 할 떄 자바스크립트 내부적으로 기본형을 
String 객체로 래핑하고 메서드를 호출하고 래핑한 객체를 버림
---
let str = "Hello";
console.log(str.charAt(0)); // "H"
---

---
let str = "Hello";
let obj = new String(str); // 내부적으로 String 객체 생성
console.log(obj.charAt(0)); // charAt() 메서드 호출
obj = null; // String 객체 버리기
---
자바스크립트 엔진은 내부적으로 String 객체를 만들어서 charAt() 메서드를 호출하고, 사용이 끝나면 다시 기본형으로 자동으로 변환하면서 개발자는 신경 안써도됨.

String 객체는 오직 자기 자신하고만 동일하다.
 - 'hello' === new String('hello);  //false
 첫 번째 예시인 'hello' === new String('hello')에서는 'hello'가 문자열 기본형이고 new String('hello')는 String 객체이기 때문에, 비교할 때 타입이 다르므로 엄격한 비교(strict comparison)를 수행합니다. 이때 자바스크립트는 기본형과 객체 타입을 비교할 때, 객체 타입을 기본형으로 변환한 후에 비교합니다. 따라서 위의 비교는 다음과 같이 처리됩니다.
 - new String('hello') === new String('hello'); //false
 두 번째 예시인 new String('hello') === new String('hello')에서는 두 개의 String 객체를 비교합니다. 이때도 엄격한 비교를 수행하므로, 두 객체가 서로 다른 객체이기 때문에 비교 결과는 false가 됩니다.

따라서, 자바스크립트에서는 기본형과 객체 타입 간의 비교에 주의해야 하며, 객체 타입을 생성할 때는 가능하면 기본형을 사용하는 것이 좋습니다.

콘솔창에서 해볼것
자바스크립트에서는 기본형을 객체로 변환 한 후 마지막에 기본형으로 다시 바꿔줌
x = 'hello'
'hello'
x.language = 'English'
'English'
x.language
undefined

String은 직접 사용하거나 인스턴스를 생성하는것은 피해야함.
타입스크립트는 객체 래퍼 타입을 지양하고, 대신 기본형 타입을 사용해야 한다.
String -> string Number -> number Boolean -> boolean Symbol -> symbol Bigint -> bigint
