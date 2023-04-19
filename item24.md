#일관성 있는 별칭 사용하기
```
const borough = {name: 'Brooklyn', location: [40.688, -73.979]};
const loc = borough.location;  -> loc라는 별칭을 만듬
loc[0] = 3;
console.log(borough.location) -> [3,-73.979]
```
위에서 loc라는 별칭을 만들고 값을 수정을 하면 borough객체의 값도 변하게 된다. loc는 borough라는 객체를 얕은복사를 한것입니다.
모든 언어의 컴파일러 개발자들은 별칭 사용으로 힘들어 합니다. 별칭 사용은 신중히 사용하여야 한다.
그래야 코드를 잘 이해할 수 있고, 오류도 쉽게 찾을 수 있습니다.
```
interface Coordinate {
  x: number;
  y: number;
}

interface BoundingBox {
  x: [number, number];
  y: [number, number];
}

interface Polygon {
  exterior: Coordinate[];
  holes: Coordinate[][];
  bbox?: BoundingBox;
}
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  if (polygon.bbox) {
    if (pt.x < polygon.bbox.x[0] || pt.x > polygon.bbox.x[1] ||
        pt.y < polygon.bbox.y[1] || pt.y > polygon.bbox.y[1]) {
      return false;
    }
  }

  // ... more complex check
}
```
bbox는 필수가 아닌 최적화 속성입니다. bbox 속성을 사용하면 어떤 점이 다각형에 포함 되는지 빠르게 체크 할 수 있다.
isPointInPolygon 함수는 매개변수로 Polygon타입인 polygon Coordinate 타입인 pt 라는 값을 받습니다.
그리고 polygon.bbox 가 있을때 조건물을 실행합니다. 두개의 x, y좌표를 받아서 점이 밖에 있는지 안에 있는지 판단하는 함수입니다.

위의 코드는 반복적인 코드가 존재합니다 polygon.bbox 라는 값이 5번이나 존재 그러니 변수로 선언을 하여 사용하여 리팩토링이 가능하다.

```
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  const box = polygon.bbox;
  if (polygon.bbox) {
    if (pt.x < box.x[0] || pt.x > box.x[1] ||
        //     ~~~                ~~~  Object is possibly 'undefined'
        pt.y < box.y[1] || pt.y > box.y[1]) {
        //     ~~~                ~~~  Object is possibly 'undefined'
      return false;
    }
  }
  // ...
}
```
위의 코드는 잘 동작이 되지만 편집기에선 오류를 발생합니다. 그 이유는 polygon.bbox를 별도의 box라는 별칭을 만들었고 위의 예제에서 잘 동작했던 제어 흐름 분석을 방해 하였기
때문입니다.

box 와 polygon.bbox의 타입을 조사해보면
```
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  polygon.bbox  // Type is BoundingBox | undefined
  const box = polygon.bbox;
  box  // Type is BoundingBox | undefined
  if (polygon.bbox) {
    polygon.bbox  // Type is BoundingBox
    box  // Type is BoundingBox | undefined
  }
}
```
속성 체크는 polygon.bbox의 타입을 정제했지만 box는 그렇지 않았기 때문에 오류가 발생 하였습니다.
이러한 오류는 별칭은 일관성 있게 사용한다는 기본원칙을 지키면 방지 할 수 있습니다.
```
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  const box = polygon.bbox;
  if (box) {
    if (pt.x < box.x[0] || pt.x > box.x[1] ||
        pt.y < box.y[1] || pt.y > box.y[1]) {  // OK
      return false;
    }
  }
  // ...
}
```
if문 조건문안에 기존에는 polygon.bbox를 넣고 box를 사용하여 오류가 생겼지만 별칭을 일관성 있게 사용을 하여 오류를 방지 할 수 있다.


#객체 비구주화를 이용할 때는 두 가지를 주의해야한다
```
interface Coordinate {
  x: number;
  y: number;
}

interface BoundingBox {
  x: [number, number];
  y: [number, number];
}

interface Polygon {
  exterior: Coordinate[];
  holes: Coordinate[][];
  bbox?: BoundingBox;
}
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  const {bbox} = polygon;
  if (bbox) {
    const {x, y} = bbox;
    if (pt.x < x[0] || pt.x > x[1] ||
        pt.y < x[0] || pt.y > y[1]) {
      return false;
    }
  }
  // ...
}
```
polygon.bbox를 비구조화 할당으로 변경 되었습니다. 하지만 x 와 y 는 선택적 속성일 경우 속성 체크가 더 필요하다.
타입의 경계에 null 값을 추가하는 것이 좋다.(아이템31)
bbox에는 선택적 속성이 적합하지만 holes는 그렇지 않다. holes가 선택적이라면 값이 없거나 빈 배열이어야 한다. 차이가 없는데 이름을 구별한 것이다.
빈 배열은 holes 없음을 나타내는 좋은 방법 이다.... -> 무슨 말인지 참... 코드를 봅시다
별칭은 타입 체커뿐만 아니라 런타임에도 혼동을 야기 할 수 있다.
```
const {bbox} = polygon;
if(!bbox) {
  calculatePolygonBbox(polygon); // polygon.bbox 값이 채워짐
  // polygon.bbox 와 bbox는 서로 다른 값을 참조 -> 얕은복사를 하여 서로 다른 메모리를 바라보게된다.
}
```
```
// HIDE
const polygon: Polygon = { exterior: [], holes: [] };
function calculatePolygonBbox(polygon: Polygon) {
  polygon.bbox = {x:[0,1],y:[2,3]}
}
// END
const {bbox} = polygon;
if (!bbox) {
  calculatePolygonBbox(polygon);  // Fills in polygon.bbox
  // Now polygon.bbox and bbox refer to different values!
  console.log(polygon.bbox,bbox) -> {x:[0,1],y:[2,3]}, undefined
}
```
polygon.bbox는 calculatePolygonBbox 로 인해서 값이 생겼지만 bbox는 구조분해를 하여 값이 없기 때문에 undefined가 도출 하게 된다.


타입스크립트의 제어 흐름 분석은 지역 변수에는 잘 동작한다. 그러나 객체 속성에서는 주의 해야합니다.