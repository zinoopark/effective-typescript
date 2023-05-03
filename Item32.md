**Item 32 유니온의 인터페이스보다는 인터페이스의 유니온을 사용하기**



요약: 
- 유니온 타입의 속성을 여러개 가지는 인터페이스에서는 속성 간의 관계가 분명하지 않다.
- 인터페이스의 유니온이 더 정확하고 타입스크립트가 이해하기에도 좋다.
- 인터페이스의 유니온은 태그된 유니온에서 쓰인다.  [[태그된 유니온]]은 타입스크립트와 매우 잘 맞기 때문에 자주 볼 수 있는 패턴이다.

유니온 타입의 속성을 갖는 인터페이스를 작성할 때, 인터페이스의 유니온 타입을 사용하는 것이 낫지 않은지 검토해 보아야 함.

예시)
```ts

interface Layer {
	layout: FillLayout | LineLayout | PointLayout;
	paint: FillPaint | LinePaint | PointPaint;
}
```

위의 경우, layout 프로퍼티는 모양이 그려지는 방법과 위치를, paint 프로퍼티는 스타일을 제어한다.
쉽게 말해서 하나는 브러쉬 종류, 하나는 색채우기의 방법
9개의 조합이 나올 수 있지만, 실제 사용되는 (유효한) 것은 세가지 조합이다. 예를 들어 FillLayout과 LinePaint는 동시에 있을 수 없는 타입이기 때문이다.

해결책은 각각 타입의 계층을 분리해서 인터페이스로 선언하는 것이다.

```ts
interface FillLayer {
	layout: FillLayout;
	paint: FillPaint;
}
interface LineLayer {
	layout: LineLayout;
	paint: LinePaint;
}
interface PointLayer {
	layout: PointLayout;
	paint: PointPaint;
}
```

유효한 상태만을 표현하도록 타입을 정의한 것이다.

이러한 패턴이 잘 쓰이는 곳은 `태그된 유니온`이다.
다음의 예시에서 type 프로퍼티는 문자열 리터럴 타입의 유니온이다.(태그로 사용된다)

```ts
interface Layer{
	type: 'fill' | 'line' | 'point',
	layout: FillLayout | LineLayout | PointLayout;
	point: FillPaint | LinePaint | PointPaint
}
```

위의 경우도 마찬가지로 인터페이스의 유니온으로 바꿀 수 있다.

```ts
interface FillLayer {
	type: 'fill';
	layout: FillLayout;
	paint: FillPaint;
}
interface LineLayer {
	type: 'line';
	layout: LineLayout;
	paint: LinePaint;
}
interface PointLayer {
	type: 'point'
	layout: PointLayout;
	paint: PointPaint;
}
```

여기서 type은 태그된 유니온으로서 런타임 때 어떤 타입의 Layer가 사용되는지 판단할 때 쓰인다.

예시)
```ts
function drawLayer(layer: Layer) {
	if (layer.type === 'fill')  {
		const {paint} = layer;
		const {layout} = layer;
	} else if(layer.type === 'line'){
		const {paint} = layer;
		const {layout} = layer;
	} else {
		const {paint} = layer;
		const {layout} = layer;
	}
}
```

## 타입스크립트 타입체커와 잘 맞는 태그된 유니온

- 태그된 유니온으로 데이터 타입을 표현할 수 있다면, 보통 그렇게 하는 편이 나음
- 여러개의 선택적 필드가 동시에 값이 있거나 동시에 undefined일때도 태그된 유니온이 맞음.

### 1. 여러개의 선택적 필드가 세트일 때

예시)
```ts
interface Person {
	name: string;
	// 다음은 세트다
	placeOfBirth?: string;
    dateOfBirthe?: Date;
}
```

위의 두 필드는 내용상 서로 관련된 것이지만 타입정보에는 그런 관계가 나타나지 않고 있다.

그래서 두개의 속성을 하나의 객체로 모으는 것이 더 낫다.

```ts
interface Person {
	name: string;
	birth?: {
		place: string;
		date: Date;
		}
}
```

위와 같이 하면 둘 중 하나만 없을 경우 오류가 나게 된다.

Person 객체를 매개변수로 받는 함수는 birth 하나만 체크하면 된다.

```ts
function eulogize(p: Person) {
	console.log(p.name)
	const {birth} = p;
	if (birth) {
		console.log(`was born on ${birth.date} in ${birth.place}`)	
	}
}
```


### 2. 타입의 구조를 손 댈 수 없는 상황일 때

만약 API의 결과 등, 타입의 구조를 프론트엔드 단에서 손댈 수 없는 상황일 때도 인터페이스의 유니온을 이용하여 속성 사이의 관계를 표시해줄 수 있다.

```ts
interface Name {
	name: string;
}

interface PersonWithBirth extends Name {
	placeOfBirth: string;
	dateOfBirth: Date;
}

type Person = Name | PersonWithBirth;
```


```ts
function eulogize(p: Person) {
	if ('placeOfBirth' in p) {
		p                           // 타입이 PersonWithBirth
		const {dateOfBirth}	= p     // 정상, 타입이 Date
	}
}
```