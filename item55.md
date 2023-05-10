요약:
DOM에는 타입 계층 구조가 있다.
Node, Element, HTMLElement, EventTarget 간의 차이점, 그리고 Event와 MouseEvent의 차이점을 알자.
DOM 엘리먼트와 이벤트에는 충분히 구체적인 타입 정보를 사용하거나, 타입스크립트가 추론할 수 있도록 문맥 정보를 활용해야 한다.





설명:

DOM은 Document Object Model의 약자이다. DOM에는 다양한 타입이 존재하고, 이들 간에는 계층 구조가 있다. 이를 이해하기 위해 몇가지 중요한 타입에 대해서 알아보자.

-   **Node**: DOM의 가장 기본이 되는 타입으로, 웹 페이지의  모든 DOM 요소는 Node로부터 상속받아 만들어진다.
    
-   **Element**: Node를 상속받은 타입 중 하나로, 특정한 종류의  예를 들어, `<div>`, `<p>`, `<span>` 등은 모두 Element이다.
    
-   **HTMLElement**: Element를 상속받은 타입 중 하나로, HTML 문서에서 특정한 HTML 요소를 나타낸다. 예를 들어, `<div>`, `<p>`, `<span>`과 같은 HTML 요소들은 HTMLElement다.
    
-   **EventTarget**: 이벤트를 처리할 수 있는 객체를 나타내는 인터페이스다. 이벤트를 발생시키고 이벤트 리스너를 등록하고 제거하는 등의 기능을 제공한다.
    
-   **Event**: 사용자 액션이나 시스템에서 발생한 이벤트를 나타내는 객체다. 예를 들어, 클릭, 마우스 이동, 키 입력 등은 이벤트에 해당한다.
    
-   **MouseEvent**: 마우스와 관련된 이벤트를 나타내는 객체로, 마우스 버튼의 클릭, 이동, 드래그 등의 동작에 대한 정보를 제공한다.



추가 자료1:

### 대표적인 DOM Types
-   `HTMLElement`: HTML 요소를 나타내며, 모든 HTML 요소 타입의 기본 클래스로 사용된다.
-   `HTMLDivElement`: `<div>` 
-   `HTMLAnchorElement`: `<a>` 
-   `HTMLInputElement`: `<input>` 
-   `HTMLButtonElement`: `<button>` 
-   `HTMLImageElement`: `<img>` 
-   `HTMLAudioElement`: `<audio>` 
-   `HTMLVideoElement`: `<video>` 
-   `HTMLCanvasElement`: `<canvas>` 
-   `HTMLTableElement`: `<table>` 
-   `HTMLFormElement`: `<form>` 
-   `HTMLSelectElement`: `<select>`
-   `HTMLTextAreaElement`: `<textarea>` 