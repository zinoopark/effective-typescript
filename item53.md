자바스크립트의 고질적인 문제: 버그로 치부할 만한 문법도 호환성 때문에 어쩔 수 없이 절대로 사라지지 않고 언어에 남아있음(예: var)

타입스크립트에도 초기에 만들어진 기능 중 혼란을 야기할 만한 기능들이 몇 개 있다.


사용을 지양해야할 타입스크립트의 대표적인 기능들

1. enum
2. 매개변수 속성
3. 네임스페이스와 트리플 슬래시 임포트
4. 데코레이터



사용하면 안되는 이유

1. enum : 상황에 따라서 다르게 동작해서 혼란스러움

- 숫자 열거형의 문제: 숫자를 맘대로 할당할 수 있어서 혼란
	- 숫자 열거형은 기본적으로 0부터 시작해서 1씩 증가하는 숫자가 할당된다. 그런데 수동으로 다른 숫자를 할당할 수 있다. 이게 가능한 이유는 enum이 애초에 비트 플래그 구조를 표현하기 위해서 설계된 기능이기 때문이다. 문제는 이와 같이 순서와 상관 없는 숫자를 할당하면 사용할 때 헷갈릴 수 있다는 것이다. 참고로 이를 해결하기 위해서는 숫자를 할당하지 않고 의미있는 문자열을 할당할 수 있다.


- 상수 열거형의 문제: 컴파일 하고 나면 열거형의 멤버를 없애버리고 직접 대체해버림
	- 상수 열거형 (const enum)을 사용하면 컴파일러가 값을 바꿔버린다. 왜냐하면 상수 열거형은 성능과 번들 크기의 최적화를 위해서 일반적인 열거형의 멤버가 메모리 공간을 낭비하는 것을 막기 위해서 열거형 멤버들을 제거하고 해당 값을 직접 대체하기 때문이다. 
```
// 일반적인 열거형
enum Flavor {
VANILLA = 0,
CHOCOLATE = 1,
STRAWBERRY = 2,
}

let flavor = Flavor.CHOCOLATE

console.log(flavor)
console.log(Flavor)
console.log(Flavor[0])

// 콘솔에 출력된 로그

[LOG]: 1  
---
[LOG]: { "0": "VANILLA", "1": "CHOCOLATE", "2": "STRAWBERRY", "VANILLA": 0, "CHOCOLATE": 1, "STRAWBERRY": 2 }  
---
[LOG]: "VANILLA"

// 상수 열거형
const enum Flavor {
VANILLA = 0,
CHOCOLATE = 1,
STRAWBERRY = 2,
}

let flavor = Flavor.CHOCOLATE

console.log(flavor)
console.log(Flavor)
console.log(Flavor[0])

// 콘솔에 출력된 로그
[LOG]: 1  
---
[ERR]: "Executed JavaScript Failed:"  
---
[ERR]: Flavor is not defined


// 결론 : 예상과 다르게 동작함
```


- 문자열 열거형의 문제: 명목적 타이핑을 쓰므로 타입 할당 방식이 복잡해짐 -> 결국 자바스크립트와 타입스크립트에서 사용방법이 달라짐
	- 타입스크립트에서 일반적으로 구조적 타이핑을 쓰는데 열거형은 명목적 타이핑을 씀. 명목적 타이핑은 타입의 이름이 같아야만 할당이 허용되는 것으로, 문자열 열거형을 매개변수로 사용한다고 하면, 열거형을 임포트 하고 문자열 대신 사용해야함
```
function scoop(flavor: Flavor) {/* ... */}

// 위와 같은 함수가 있다고 할때, 자바스크립트에서는 다음처럼 문자열로 호출가능함

scoop('vanilla');

// 하지만 타입스크립트에서는 명목적 타이핑을 위해서 열거형을 임포트 한다음에 위의 문자열 대신에 해당 열거형을 넣어줘야함.

import {Flavor} from 'ice-cream'
scoop(Flavor.VANILLA)

```

해결 방안 : 리터럴 타입의 유니온을 쓰자 (안전함 + 자바스크립트 호환성 + 자동완성기능)


2. 매개변수 속성

매개변수 속성이라는 간결한 문법이 있음. 그러나 다음의 문제가 있음

1. 컴파일을 하면 코드가 오히려 늘어남
2. 런타임에 실제로 사용되는데 타입스크립트 관점에서 보면 사용되지 않는 것처럼 보임
3. 일반 속성과 섞어 쓰면 혼란스러워짐

```
class Person {
    first: string;
    last: string;
    constructor(public name: string) {
        [this.first, this.last] = name.split(' ');
    }
}
```
매개변수 속성이란 클래스의 생성자 매개변수 앞에 접근 제한자(public, private, protected)를 함께 사용해서 선언하는 방식. 이렇게 하면 클래스 내에서 해당 매개변수를 멤버 변수로 선언하고 동시에 초기화 할 수 있다. 위의 예제에서 name 매개변수는 클래스 내에서 멤버 변수로 선언되면서 동시에 생성자에서 name을 사용하여 first와 last 변수를 초기화하고 있다. 

그러나 다음과 같은 문제가 생길 수 있다.

```
class Person {
    constructor(public name: string) {}
}

const p: Person = { name: 'Jed Bartlet'};

```

위의 예제를 보면 Person클래스의 인스턴스 p를 생성하면서 객체 리터럴 형태로 초기화 하고 있다. 이는 문제가 될 수 있다. 왜냐하면 Person 클래스의 생성자에서 name 매개변수를 받아와서 내부적으로 name을 멤버 변수로 할당하고 초기화하는 로직을 가지고 있는데, 위의 예제처럼 객체 리터럴로 초기화해버리면 생성자의 로직을 우회하고 직접적으로 name 속성을 할당하게 되기 때문이다. 로직을 우회한다는 건 결국 의도와 다르게 동작한다는 것이다. (이럴 때는 생성자를 사용해서 인스턴스를 생성하는 것이 좋다.-> const p = new Person('Jed Bartlet'))


3. 네임 스페이스와 트리플 슬래시 임포트
모듈 시스템이 없었을 때, 타입스크립트는 자체 모듈 시스템을 구축하였다. 
그것이 바로 : module 키워드, '트리플 슬래시' 임포트
이후에 추가된 것이 : namespace 키워드
```
namespace foo {
    fuction bar() {}
}

/// <reference path="other.ts"/>
foo.bar();

```
위의 기능들은 호환성 때문에 잔여한 것이고 이제 ES 모듈을 써야함.


4. 데코레이터: 아직 표준이 아님


데코레이터는 아직 표준이 되지 않은 기능으로, OOP 프로그래밍을 하는 다른 언어, 예를 들어 자바 같은 데에서 사용하는 annotation과 비슷한 기능이다. 어노테이션은 사전적인 의미로는 '주석' 이라는 뜻인데 구글에 검색해서 의미를 찾아보면 소스코드에 추가해서 사용할 수 있는 메타데이터의 일종이라고 나온다. 런타임에 특정 기능을 실행하도록 정보를 제공한다 정도로만 알아두면 좋을 것 같다. 자바스크립트에서 데코레이터를 사용하면 데코레이터를 사용하는 곳에서 특정 함수를 실행시킬 수 있는데, 반복되는 작업을 모듈화하는 데 유용할 수 있다.

실제로 자바스크립트에 도입된 것도 OOP 프로그래밍을 할 때 사용하기 위해 만들어진 것인데, 문제는 애초에 OOP 프로그래밍이 강제된 언어가 아니다보니 대체제가 많아서 해당 기능이 표준화 되어야 하는지 않아야 하는지에 대해서는 의견이 분분하다. 게다가 Mobx에서 주로 사용되면서 자주 쓰였는데, 이제는 Mobx에서 데코레이터 없이도 쓸 수 있게 되어서 데코레이터의 사용빈도가 줄게 되었다. 따라서 표준이 될 가능성이 적으니 사용하지 말라는 것이 책의 요지이다.

그래도 흥미로운 기능이라고 생각하는데, 클래스 문법에서 자주 쓰이는 bind()를 데코레이터를 이용해서 따로 함수로 뺀다음에 간단하게 this를 바인딩 할 수 있기 때문이다. 다음처럼 하면 된다. (실험적 기능이라서 사용하려면 tsconfig에 `"experimentalDecorators": true` 추가해야한다.)

```
function autobind(  
  _: any,  
  _2: string,  
  descriptor: PropertyDescriptor  
) {  
  const originalMethod = descriptor.value;  
  const adjDescriptor: PropertyDescriptor = {  
    configurable: true,  
    get() {  
      const boundFn = originalMethod.bind(this);  
      return boundFn;  
    }  
  };  
  return adjDescriptor;  
}

// 위와 같이 autobind 함수를 만든뒤에 바인딩이 필요한 메서드 바로 윗줄에 @autobind라는 라인을 추가하기만 하면 this 바인딩이 된다. autobind 함수의 파라미터는 항상 target, methodName, descriptor 이렇게 세개를 받는데, 실제로 target과 methodName은 함수내에서 사용되는 곳이 없기 때문에, 타입스크립트에서는 사용되지 않는 파라미터가 있다는 오류를 뿜는다. 이럴때, 언더바를 넣어주면 해당 파라미터를 받기는 하는데 사용은 안한다는 힌트를 타입스크립트한테 줄 수 있어서 오류가 나지 않게 된다.

// 다음은 실제 사용사례

class ProjectInput {  
  templateElement: HTMLTemplateElement;  
  hostElement: HTMLDivElement;  
  element: HTMLFormElement;  
  titleInputElement: HTMLInputElement;  
  descriptionInputElement: HTMLInputElement;  
  peopleInputElement: HTMLInputElement;  
  
  constructor() {  
    this.templateElement = document.getElementById(  
      'project-input'  
    )! as HTMLTemplateElement;  
    this.hostElement = document.getElementById('app')! as HTMLDivElement;  
  
    const importedNode = document.importNode(  
      this.templateElement.content,  
      true  
    );  
    this.element = importedNode.firstElementChild as HTMLFormElement;  
    this.element.id = 'user-input';  
  
    this.titleInputElement = this.element.querySelector(  
      '#title'  
    ) as HTMLInputElement;  
    this.descriptionInputElement = this.element.querySelector(  
      '#description'  
    ) as HTMLInputElement;  
    this.peopleInputElement = this.element.querySelector(  
      '#people'  
    ) as HTMLInputElement;  
  
    this.configure();  
    this.attach();  
  }  
  
  @autobind  // 여기서 쓰였음
  private submitHandler(event: Event) {  
    event.preventDefault();  
    console.log(this.titleInputElement.value);  
  }  
  
  private configure() {  
    this.element.addEventListener('submit', this.submitHandler);  
  }  
  
  private attach() {  
    this.hostElement.insertAdjacentElement('afterbegin', this.element);  
  }  
}  
  
const prjInput = new ProjectInput();

// 테스트 해보고 싶으면 다음의 html과 함께 해보면 됨

<!DOCTYPE html>  
<html lang="en">  
  <head>  
    <meta charset="UTF-8" />  
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />  
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />  
    <title>ProjectManager</title>  
    <link rel="stylesheet" href="app.css" />  
    <script src="dist/app.js" defer></script>  
  </head>  
  <body>  
    <template id="project-input">  
      <form>  
        <div class="form-control">  
          <label for="title">Title</label>  
          <input type="text" id="title" />  
        </div>  
        <div class="form-control">  
          <label for="description">Description</label>  
          <textarea id="description" rows="3"></textarea>  
        </div>  
        <div class="form-control">  
          <label for="people">People</label>  
          <input type="number" id="people" step="1" min="0" max="10" />  
        </div>  
        <button type="submit">ADD PROJECT</button>  
      </form>  
    </template>  
    <template id="single-project">  
      <li></li>  
    </template>  
    <template id="project-list">  
      <section class="projects">  
        <header>  
          <h2></h2>  
        </header>  
        <ul></ul>  
      </section>  
    </template>  
    <div id="app"></div>  
  </body>  
</html>
```