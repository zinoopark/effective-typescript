# 타입좁히기

타입좁히기 : 타입스크립트가 넓은 타입으로부터 좁은 타입으로 진행하는 과정을 말한다.

- 일반적인 예시 : null 체크

```javascript
const el = document.getElementById('foo') // Type is HTMLElement | null
if (el) {
 el // Type is HTMLElement
 el.innerHTML = 'Party Time'.blink()
} else {
 el // Type is null
 alert('No element #foo')
}
```

타입 체커는 조건문에서 타입 좁히기를 잘 해내지만 타입 별칭이 존재한다면 그러지 못할 수도 있다.

타입별칭 : 타입스크립트에서 새로운 타입을 정의하는 방법 중 하나입니다. 타입 별칭은 이미 존재하는 타입에 대한 별칭을 만들어주는 기능으로, 더 직관적이고 가독성이 좋은 코드를 작성하는데 도움이 된다.

타입 별칭은 'type' 키워드를 사용하여 정의

```javascript
type TypeName = Type; -> TypeName은 새로운 타입의 이름이고 Type은 해당 타입의 정의 / 예를 들어 코드를 만들어보면

type MyString = string;
    -타입이름-   -해당 타입의 정의-

const greeting: MyString = 'Hello, world'; -> 타입 별칭은 복잡한 타입을 간결하게 표현할 수 있는 장점이 있으며, 코드 유지보수성을 높이는데 도움을 줌
```

분기문에서 예외를 던지거나 함수를 반환하여 블록의 나머지 부분에서 변수의 타입을 좁힐 수 있다.

```javascript
const el = document.getElementById('foo') // Type is HTMLElement | null
if (!el) throw new Error('Unable to find #foo') -> el이 없다면 에러반환
el // Now type is HTMLElement  -> 위의 조건이 통과한다면 HTMLElement
el.innerHTML = 'Party Time'.blink()
```

instanceof를 사용한 타입좁히기

```javascript
function contains(text: string, search: string | RegExp) {
  if (search instanceof RegExp) { -> search과 정규표현식이라면?
    search // Type is RegExp -> 정규표현식
    return !!search.exec(text)
  }
  search // Type is string -> if문 통과가 안되었으니 search는 당연하게 string이 된다.
  return text.includes(search)
}

```

속성체크 타입좁히기

```javascript
interface A {
  a: number
}
interface B {
  b: number
}
function pickAB(ab: A | B) {
  if ('a' in ab) {  -> ab에 a가있느냐?
    ab // Type is A -> 그렇다면 타입a
  } else {
    ab // Type is B -> 아니라면 타입b
  }
  ab // Type is A | B  -> if문이면 a else면 b 
}

```

Array.isArray 내장함수 타입좁히기
```javascript
function contains(text: string, terms: string | string[]) {
  const termList = Array.isArray(terms) ? terms : [terms]  -> terms가 배열이면 terms 아니라면 배열을 만들어라 / 결국엔 참일떈 terms는 string[]이고 거짓일떈 terms는 string이다
  termList // Type is string[]
  // ...
}

```

타입스크립트는 일반적으로 조건문에서 타입을 좁히는 데 매우 능숙하지만 타입을 섣불리 판단하는 실수를 저지르기 쉬우므로 다시 한번 꼼꼼히 따져야한다.

유니온 타입에서 null을 제외하기 위해 잘못된 방법 예제
```javascript
const el = document.getElementById('foo') // type is HTMLElement | null
if (typeof el === 'object') {  -> javascript에서 타입을 체크할때 쓰는 typeof / el은 엘리먼트가 있거나 null 일수도 있다.
  el // Type is HTMLElement | null -> typeof 는 null을 object로 판단한다.
}
```

기본형이 잘못되었을때 문제가 발생
```javascript
function foo(x?: number | string | null) { -> x가 있을수도 있고 없을수도 있다 그렇다면 string , number, null, undefined가 올수 있다.
  if (!x) { -> 그런데 not이 붙으면 타입추론할수 있는게 undefined / null / 0 / ""
    x // Type is string | number | null | undefined
  }
}
```

명시적 "태그"를 붙여 타입좁히기
```javascript
interface UploadEvent {
  type: 'upload'           -> type으로 비교를 하게 된다.  아이템3에서 배운바 있음.
  filename: string
  contents: string
}
interface DownloadEvent {
  type: 'download'
  filename: string
}
type AppEvent = UploadEvent | DownloadEvent

function handleEvent(e: AppEvent) {
  switch (e.type) {
    case 'download':
      e // Type is DownloadEvent
      break
    case 'upload':
      e // Type is UploadEvent
      break
  }
}

```
위의 코드를 태그된 유니온 또는 구별된 유니온 이라고 불린다.

만약 타입스크립트가 타입을 식별하지 못한다면, 식별을 돕기 위해 커스텀 함수를 도입이 가능하다.
```javascript
function isInputElement(el: HTMLElement): el is HTMLInputElement {
  return 'value' in el
}

function getElementContent(el: HTMLElement) {
  if (isInputElement(el)) {
    el // Type is HTMLInputElement
    return el.value
  }
  el // Type is HTMLElement
  return el.textContent
}
```
위의 코드는 HTMLElement와 HTMLInputElement의 차이를 처리하는 방법을 보여주는 예제입니다.

'isInputElement' 함수는 el 파라미터가 HTMLInputElement 타입인지 여부를 판단 하는 함수이고
이 함수는 el is HTMLInputElement와 같은 형태로 반환 타입을 지정하고 반환값은 불리언이 됩니다. 반환값이 true이면 el은 HTMLInputElement 타입입니다.

isInputElement 함수는 타입가드를 뜻하고 반환값은 el is HTMLInputElement 형태로 타입 단언을 통해 타입을 확정하는 불리언 값.

'value' in el 표현식은 el 객체에 value 프로퍼티가 있는지 검사를 하고 만약 value 프로퍼티가 존재하면 타입스크립트는 el 객체를 HTMLInputElement 타입으로 추론이 가능하다.

이렇게 구현된 isInputElement 함수는 el이 HTMLInputElemnet 타입인지 여부를 검사하고 타입이 맞으면 true를 반환 따라서 isInputElement 함수가 true를 반환하는 경우 el이 

HTMLInputElemnet 타입임을 확신이 가능하다.  이렇게 타입 가드를 사용하면 타입 안정성을 높일 수 있다.

```javascript
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael']
const members = ['Janet', 'Michael'].map(who => jackson5.find(n => n === who)) // Type is (string | undefined)[]
```

```javascript
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael']
const members = ['Janet', 'Michael'].map(who => jackson5.find(n => n === who)).filter(who => who !== undefined) // Type is (string | undefined)[]
```
['Janet', 'Michael'] map 함수를 사용하여 jackson5의 배열안에서 동일한것을 찾는 코드인데 타입추론은  (string | undefined)[] 이 나오게 되는데 filter를 써서 undefined를 걸러내도 작동이 되지 않는다.

이떄 타입가드를 사용하면 타입을 잡을수 있다.
```javascript
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael']
function isDefined<T>(x: T | undefined): x is T { -> <T>가 어떤 타입이든 올수 있고 x에는 T | undefined가 올수있는걸로 정의 그리고 x가 is T이면 
  return x !== undefined
}
const members = ['Janet', 'Michael'].map(who => jackson5.find(n => n === who)).filter(isDefined) // Type is string[]
```
먼저, jackson5는 문자열 타입의 배열입니다. isDefined 함수는 제네릭 타입 T를 매개변수로 받으며, 반환값은 x is T 형태로 타입 단언을 통해 T 타입인지 여부를 검사하는 불리언 값입니다. 이 함수는 x 값이 undefined가 아니면 true를 반환합니다.

members는 ['Janet', 'Michael'] 배열에 대해 jackson5 배열에서 찾은 결과를 필터링한 배열입니다. map 함수를 사용하여 ['Janet', 'Michael'] 배열의 각 요소에 대해 jackson5 배열에서 n === who 조건을 만족하는 값을 찾고, 
filter 함수를 사용하여 undefined 값이 아닌 요소만 걸러냅니다.

이 때, filter 함수에서 isDefined 함수를 사용하여 undefined 값을 제거합니다. isDefined 함수는 제네릭 타입을 사용하므로, members 배열의 타입은 string[]로 추론이 됩니다.

따라서, members 배열의 타입은 string[]이 됩니다.
