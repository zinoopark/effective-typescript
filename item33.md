**Item 33 string 타입보다 더 구체적인 타입 사용하기**



- 툭하면 문자열 타입을 쓰지 말자. 모든 문자열을 할당할 수 있는 string타입보다는 더 구체적인 타입을 쓰자
- 변수의 범위를 정확하게 표현하고 싶으면 문자열 리터럴 타입의 유니온을 사용하라. 타입체크가 엄격해지고 생산성이 향상된다.
- 객체의 속성 이름을 함수 매개변수로 받을 때는 string 말고 keyof T를 사용하자.

### string은 범위가 너무 넓다

좁은 타입을 쓰도록 하자. 

```ts
interface Album {
	artist: string;
	title: string;
	releaseDate: string; // YYYY-MM-DD
	recordingType: string; // 소문자로
}
```

주석에 구체적인 요구사항이 있지만 string의 경우 범위가 너무 넓어서 요구사항 외의 것들도 타입체커를 통과한다.

또한 다 똑같이 string이어서 매개변수 순서가 바뀌어도 오류를 내지 않게 된다.

다음과 같이 바꿀 수 있다

```ts
type RecordingType = 'studio' | 'live' // 어차피 선택지는 두개이므로 문자열 리터럴로 범위를 좁혔음

interface Album {
	artist: string;
	title: string;
	releaseDate: Date;
	recordingType: RecordingType;
}
```


이런 방식의 장점:
1. 타입을 명시적으로 정의하여 다른 곳으로 값이 전달되어도 타입 정보가 유지됨.

 예를 들어 다음과 같은 함수가 있다고 치자.
```ts
function getAlbumsOfType(recordingType: string) : Album[] {
//...레코딩 타입으로 분류해서 모든 앨범을 가져오는 함수
}
```

getAlbumsOfType을 호출하는 곳에서는 recordingType이 문자열이라는 것 외의 정보를 알수가 없다. 주석으로 처리한 정보는 전달되지 않기 때문에

2. 타입을 명시적으로 정의하고 해당 타입의 의미를 설명하는 주석을 붙여 넣을 수 있다.(item 48)

```ts
/** 이 녹음은 어떤 환경에서 이루어졌는지? **/ // 이렇게 주석을 작성해 놓으면 나중에 함수를 사용하는 곳에서 해당 타입에 마우스 호버를 하면 보인다.
type RecordingType = 'live' | 'studio'
```


3. keyof 연산자로 객체의 속성 체크를 더 세밀하게 할 수 있다.
예시)
```ts
function pluck(records, key){
	return records.map(r => r[key])
}
// 어떤 배열에서 한 필드의 값만 추출하는 함수
```

위 함수의 시그니처를 다음처럼 작성할 수 있다.

```ts
function pluck(records: any[], key: string): any[] {
	return records.map(r => r[key])
}
```

하지만 위처럼 하면 any타입이 있어서 정밀하지 못하다. 반환값에 any를 쓰는 것은 좋지 않다.(item 38)
다음처럼 제너릭 타입을 도입해서 시그니처 개선을 시도해보자.

```ts
function pluck<T>(records: T[], key: string): any[] {
	return records.map(r => r[key]);
						// ~~~~~~~ '{}' 형식에 인덱스 시그니처가 없으므로 요소에 암시적으로 'any' 형식이 있습니다.
}
```

이렇게 되면 key의 타입이 string이기 때문에 범위가 너무 넓다는 오류가 발생한다.
Album의 배열을 매개변수로 전달하면 key는 "artist", "title", "releaseDate", "recordingType" 네개의 값만이 유효하다. 이는 keyof Album 타입으로 얻게 되는 결과와 같다.
그러므로 string을 keyof T로 바꾸면 된다.

```ts
function pluck<T>(records: T[], key: keyof T) {
	return records.map(r => r[key]);
}
```

하지만 여기서 key의 값으로 하나의 문자열을 넣게 되면 그 범위가 너무 넓다. 

예시)
```ts
const releaseDates = pluck(albums, 'releaseDate'); // 타입이 (string | Date)[]
```
여기서 releaseDate의 타입은 Date[]여야 한다.

따라서 더 좁히기 위해서 keyof T의 부분집합으로 두 번째 제너릭 매개변수를 추가해준다.

```ts
function pluck<T, K extends keyof T>(records: T[], key: K): T[K][] {
	return records.map(r=> r[key]);
}
```

(제너릭 관련 추가설명)

'K'는 'T'의 key를 확장하는 제너릭 타입 파라미터이다.

- 'keyof T'는 TypeScript에서 사전 정의된 키워드로, 타입 'T'의 모든 속성 키를 유니온 타입으로 생성한다. 예를 들어 타입 'Person'{name: string; age: number} 이 있다면, 'keyof Person'은 "name" | "age"가 된다.
- 'K'는 타입 'T'의 특정 key를 나타내는 제네릭 타입 파라미터이다. 'keyof T'를 확장하여 'K'가 'T' 타입 중 실제로 존재하는 key만 사용할 수 있도록 한다.
- `T[K]`는 타입 `T`에서 이름이 `K`인 속성의 타입을 반환하는 타입 쿼리이다. 예를 들어 `T`가 `{name: string, age: number}` 이고 `K`가 `'name'`이면 `T[K]`는 'string' 이다.


```ts
pluck(albums, 'releaseDate'); // 타입이 Date[]
pluck(albums, 'artist'); // 타입이 string[
pluck(albums, 'recordingType'); // 타입이 RecordingType[]
pluck(albums, 'recordingDate');
              // ~~~~~~~~~~~~ "recordingDate" 형식의 인수는 ... 형식의 매개변수에 할당될 수 없습니다
```

string은 any와 비슷한 문제를 갖고 있으니 조심해야 한다.