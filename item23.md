# 한꺼번에 객체 생성하기
 
 아이템20에서 나와있지만 변수의 값은 변경될 수 있지만, 타입스크립트의 타입은 일반적으로 변경이 불가하다. 이러한 특성 덕분에 일부 자바스크립트 패턴을 타입스크립트로 모델링하는게 쉬워진다.
 객체를 생성할 때는 속성을 하나씩 추가하기보다 여러 속성을 포함해서 한꺼번에 생성해야 타입추론에 유리하다.

 ```
 const pt = {}
pt.x = 3  오류가 발생
// ~ Property 'x' does not exist on type '{}'
pt.y = 4  오류가 발생 
// ~ Property 'y' does not exist on type '{}'
```
첫 번째 줄의 pt 타입은 {} 값을 기준으로 추론되기 떄문에 존재하지 않는 속성을 추가할 수 없다.

```
interface Point {
  x: number
  y: number
}
const pt: Point = {}
// ~~ Type '{}' is missing the following properties from type 'Point': x, y
pt.x = 3
pt.y = 4
```
Point 인터페이스를 정의하면 오류가 바뀐다. pt에 마우스를 올려보면 x,y속성이 처음에 정의 되지 않았다고 나오게 된다.
위의 코드는 이렇게 변형될 수 있다.
```
interface Point {
  x: number
  y: number
}
const pt = {
  x: 3,
  y: 4,
} // OK
```
그냥 객체를 한번에 정의하면 해결이 된다. 왜 책에는 타입을 넣어주지 않았을까....

```
interface Point {
  x: number
  y: number
}
const pt = {} as Point  <- 타입 단언문을 통해 지정하면 에러가 나지 않는다 이렇게 하는 것도 좋지만 객체를 한꺼번에 만드는게 더 좋다고 합니다.
pt.x = 3
pt.y = 4 // OK
```

작은 객체들을 조합해서 큰 객체를 만들어야 하는 경우에도 여러 단계를 거치는 것은 좋지 않다.

```
interface Point {
  x: number
  y: number
}
const pt = { x: 3, y: 4 }
const id = { name: 'Pythagoras' }
const namedPoint = {}
Object.assign(namedPoint, pt, id)
namedPoint.name;
// ~~~~ Property 'name' does not exist on type '{}'
```
위의 코드를 보면 Object.assign이라는 빈객체를 통해 객체를 통합한다. 하지만 namedPoint.name이나 x,y를 해도 에러가 난다. 왜냐하면 namedPoint안에는 해당 프로퍼티가 없기 떄문입니다.
그렇기 떄문에 아래의 코드처럼 작성을하면 작동이된다. ... 은 스프레드 연산자인데 객체안에 있는 값을 얕은복사를 하여 작동이 정상적으로 됩니다.
```
interface Point {
  x: number
  y: number
}
const pt = { x: 3, y: 4 }
const id = { name: 'Pythagoras' }
const namedPoint = { ...pt, ...id }
namedPoint.name // OK, type is string
```

```
interface Point {
  x: number
  y: number
}
const pt0 = {}
const pt1 = { ...pt0, x: 3 }
const pt: Point = { ...pt1, y: 4 } // OK
```
pt0 변수는 빈 객체입니다. 따라서, pt1 변수는 pt0 객체를 복사하고, x 속성을 추가한 객체가 됩니다. 이때, 스프레드 연산자를 사용하여 객체를 복사하면 얕은 복사가 수행되므로, pt1 객체는 { x: 3 } 객체를 참조하게 됩니다.

따라서, pt: Point 변수에 { ...pt1, y: 4 }를 할당하면, 스프레드 연산자를 사용하여 pt1 객체를 복사하고, y 속성을 추가한 객체가 생성됩니다. 이때, pt1 객체는 { x: 3 } 객체를 참조하고 있으므로, 복사된 객체는 { x: 3, y: 4 } 객체를 참조하게 됩니다.

마지막으로, pt 변수는 Point 인터페이스에 따라 선언된 객체 타입입니다. pt 변수가 { x: 3, y: 4 } 객체를 참조하므로, pt 변수의 타입이 Point 인터페이스를 준수하는 것이 맞습니다. 즉, { x: 3, y: 4 } 객체는 Point 인터페이스에 따라 { x: number, y: number } 형태를 가져야 하며, { x: 3, y: 4 } 객체는 이 조건을 만족하므로, pt: Point 변수에 할당 가능합니다.


책에서 130페이지 아래의 코드는 예제인데 pharaoh 심벌에 마우스를 올려 보면 타입이 유니온으로 추론 된다고 하는데 유니온으로 추론되지 않고 객체 안에서 필요한 타입만 유니온으로 잡아준다. <책선정 잘못했을지도..ㅋㅋㅋ>
```
declare elt hasDates: boolean;
const nameTitle = {name: 'Khufu',title:'Pharaoh'};
const pharaoh = {
  ...nameTitle,
  ...(hasDates ? {start: -2589, end: -2566}: {})
}
```

뒤쪽 헬퍼함수를 사용하라고 나오는데 이부분도.... 맞지 않음...