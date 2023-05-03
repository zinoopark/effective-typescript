# 콜백에서 this에 대한 타입 제공하기

> 한줄요약
1. 콜백 함수에서 this를 사용할 때, 타입 정보를 명시하자.

우선 this를 이해해야한다. this는 let, const와 다르게 lexical scope가 아닌 dynamic scope이다.
즉 `정의된 방식`이 아닌, `호출된 방식`에 따라 사용된다.

```javascript
class C{
    vals = [1,2,3];
    logSqures(){
        for (const val of this.vals){
            console.log(val * val);
        }
    }
}

const c = new C();
c.logSquares();
// 정상 작동

const method = c.logSquares();
method();
// error  Uncaught TypeError: undefined의 'vals' 속성을 읽을 수 없습니다.
```
위에서 문제는 실제로 `c.logSquares()`가 두가지 작업을 수행하기 때문에 문제가 된다. 
1. `C.prototype.logSquares` 호출,
2. this를 c로 바인딩.

그런데 `const method = c.logSquares();` 에서 참조변수를 사용하면서 문제가 발생했다.

```javascript
method.call(c);
//정상작동
```
call을 사용해 명시적으로 바인딩 할 수 있다.


```javascript
class ResetButton {
  render() {
    return makeButton({ text: "Reset", onClick: this.onClick });
  }
  onClick() {
    alert(`Reset ${this}`);
  }
}
```
ResetButton에서 onClick함수를 호출하면 this 바인딩 문제로 인해, Reset이 정의되지 않았다는 에러가 나온다.


> 해결책

1. 생성자에서 this 바인딩 -> 인스턴스 속성이 프로토타입 속성보다 lookup sequence에서 앞서는 것을 이용.

```javascript
// 생성자에서 메서드에 this 바인딩
class ResetButton {
  constructor() {
    this.onClick = this.onClick.bind(this);
  }
  render() {
    return makeButton({ text: "Reset", onClick: this.onClick });
  }
  onClick() {
    alert(`Reset ${this}`);
  }
}
```

2. 화살표함수 -> ResetButton이 생성될 때 마다, 바인딩된 this를 가지는 새 함수를 생성.
```javascript
// 화살표 함수
class ResetButton {
  render() {
    return makeButton({ text: "Reset", onClick: this.onClick });
  }
  onClick = () => {
    alert(`Reset ${this}`); 
  };
}
```

실제 생성되는 코드.
```javascript
class ResetButton {
  constructor() {
    var _this = this;
    this.onClick = function () {
        alert(`Reset ${_this}`);
    };
  }
  render() {
    return makeButton({ text: "Reset", onClick: this.onClick });
  }
}
```
-----------------------------------------


## this를 사용하는 콜백 함수가 있을 때,

```javascript
// 타입스크립트에서 this 바인딩
function addKeyListener(
  el: HTMLElement,
  fn: (this: HTMLElement, e: KeyboardEvent) => void
) {
  el.addEventListener("keydown", (e) => fn.call(el, e));
}
```

```javascript
// call을 제거하고 fn을 호출해보자
function addKeyListener(
  el: HTMLElement,
  fn: (this: HTMLElement, e: KeyboardEvent) => void
) {
  el.addEventListener("keydown", (e) => fn(el, e));  // ERROR 1개의 인수가 필요한데, 2개를 기재했습니다. 
}


function addKeyListener(
  el: HTMLElement,
  fn: (this: HTMLElement, e: KeyboardEvent) => void
) {
  el.addEventListener("keydown", (e) => fn(e));  // void 형식에 this 컨텍스트를 HTMLElement 형식의 this에 할당할 수 없습니다.
}
```

