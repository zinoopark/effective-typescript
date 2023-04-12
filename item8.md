# 타입 공간과 값 공간의 심벌 구분하기

- 심벌(symbol) : 타입 공간이나 값 공간 중의 한 곳에 존재
  심벌은 이름이 같더라도 속하는 공간에 따라 다른 것을 나타 낼 수 있다.

```
타입인것
 interface Cylinder {
   radius: number;
   height: number;
 }
변수 값 인것
 const Cylinder = (radius: number, height: number) => ({radius,height});
```

위 코드에서 Cylinder 라는 이름이 같지만 아무런 관련이 없다. -> 이렇게 사용을 한다면 가끔 오류를 발생시킨다.

```
 function calculateVolume(shape: unknown) {
   if(shape instanceof Cylinder) {
     shape.radius
       // ~~~~~~'{}' 형식에 'radius' 속성이 없습니다.
   }
 }
```

instanceof : 자바스크립트 런타임 연산자 -> instanceof Cylinder는 타입이 아니라 함수 자체를 참조를해서 한 심벌이 타입인지 값인지 언뜻 봐서는 알 수 없고 문맥을 통해 알아내야한다.
-> JavaScript에서 사용되는 연산자 중 하나로, 객체가 특정 클래스의 인스턴스인지 여부를 확인하는 데 사용

```
class Person {
  constructor(public name: string, public age: number) {}
}

const person = new Person('John', 30);
console.log(person instanceof Person); // true
```

# 타입스크립트 코드에서 타입과 값은 번갈아 나올 수 있다.

:string :number -> 타입
= {} -> 값

```
class Cylinder {
  radius = 1
  height = 1
}

function calculateVolume(shape: unknown) {
  if (shape instanceof Cylinder) {
    shape // OK, type is Cylinder
    shape.radius // OK, type is number
  }
}
```

연산자 중에서도 타입에서 쓰일 떄와 값에서 쓰일 떄 다른 기능을 하는 것 -> typeof
type 의 관점에서는 typeof는 타입스크립트 타입을 반환
값의 관점에서는 typeof는 자바스크립트 typeof 연산자로 반환 -> 값공간의 typeof는 대상 심벌의 런타임 타입을 가리키는 문자열을 반환
자바스크립트는 단8개의 런타임 타입 존재(string,number,boolean,undefined,object,function,bigint,symbol) 

```
interface Person {
  first: string
  last: string
}
const p: Person = { first: 'Jane', last: 'Jacobs' }
//    -           --------------------------------- Values
//       ------ Type
function email(p: Person, subject: string, body: string): Response {
  //     ----- -          -------          ----  Values
  //              ------           ------        ------   -------- Types
  // COMPRESS
  return new Response()
  // END
}

class Cylinder {
  radius = 1
  height = 1
}

function calculateVolume(shape: unknown) {
  if (shape instanceof Cylinder) {
    shape // OK, type is Cylinder
    shape.radius // OK, type is number
  }
}
type T1 = typeof p // Type is Person
type T2 = typeof email
// Type is (p: Person, subject: string, body: string) => Response

const v1 = typeof p // Value is "object" -> 타입스크립트 입장에서는 타입추론을 못함
const v2 = typeof email // Value is "function"
```

# 타입의 속성을 얻는법
```
interface Person {
  first: string
  last: string
}
const p: Person = { first: 'Jane', last: 'Jacobs' }

const first: Person['first'] = p['first'] // 또는 p.first
  
```
타입에서 속성을 얻을떄는 객체처럼 키를 가져오듯이 Person.first 는 적용이 되지 않는다. []를 이용해서 얻을수 있다.


# 자바스크립트 구조분해
```
  function email({person,subject,body}) {
    // ...
  }
```

# 타입스크립트 구조분해할당 오류
```
  function email({person:Person,subject:string,body:string}) {
    // ... person:Person 바인딩 요소 Person에 암시적으로 any 형식이 있습니다. -> 여기에서는 타입으로서 접근된게 아니라 값의 관점에서 해석이 되어 오류발생.
  }

  function email({person,subject,body}:{person:Person,subject:string,body:string}) {
    // ... 이런씩으로 작성해야지 문제 없이 돌아감
  }
```
