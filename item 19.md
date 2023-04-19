## 타입 추론으로 코드를 간결하게

타입 추론이 가능하기 때문에 변수를 선언할 때마다 타입을 명시하는 것은 사실 불필요하고 비생산적이다.

```typescript
let x: number = 12;
// 위와 같이 타입을 명시하는 것은 장황하다

let x = 12;
// 위처럼 하는 것으로 충분하다. 타입이 궁금하면 ide를 이용해 타입을 체크하면 된다.
```

타입 추론이 된다면 명시적 타입 구문을 사용하지 말고 코드를 간결하게 유지하는 것이 좋다. 


## 타입 추론이 가능한 경우들

타입스크립트는 어디까지 추론이 가능할까?
타입스크립트는 복잡한 **객체**, 함수가 반환하는 **배열**의 타입까지도 추론할 수 있고 더 나아가서 코더가 하는 일반적인 추론보다 더 **정확한 추론**을 해줄 때도 있다.


1. **객체**
```typescript
const person: {
  name: string;
  born: {
	  where: string;
	  when: string;
  };
  died: {
	  where: string;
	  when: string;
	  }
} = {
	name: 'Sojourner Truth',
	born: {
		where: 'Swartekill, NY',
		when: 'c.1797',
		},
	died: {
		where: 'Battle Creek, MI',
		when: 'Nov. 26, 1883'
		}
};

// 위와 같은 장황한 코드는 타입 구문을 제하고 다음과 같이 작성해도 된다.

const person = {
name: 'Sourjourner Truth',
born: {
	where: 'Swartekill, NY',
	when: 'c.1797',
},
died: {
	where: 'Battle Creek, MI',
	when: 'Nov. 26, 1883'
}
};

```

(객체 리터럴에 대한 타입추론은 아이템 21에서 추가적으로 다룰예정)

2. **배열**

```typescript
function square(nums: number[]){
	return nums.map(x => x * x);
}
const squares = square([1,2,3,4]) // 타입은 number[]

//타입 스크립트는 함수가 어떤 타입을 반환하는지도 알고 있다.
```

3. **더 정확한 추론**

```typescript
const axis1: string = 'x' // 타입은 string
const axis2 = 'y' // 타입은 'y'
```

일반적으로 axis2 변수를 string으로 예상하기 쉽다. 그래서 타입을 명시했다면 axis1처럼 string의 타입을 가졌을 것이다. 하지만 타입스크립트는 이를 'y'로 추론하여 명시했을 때보다 더 정확한 타입을 제공하고 있다. 타입 추론 덕분에 역설적으로 타입이 정확해 진 것이다.

## 타입 추론의 또 다른 장점: 리팩터링이 용이

위에서 설명한 타입 추론의 장점들 외에도 다른 장점이 있다. **리팩터링이 용이해진다**는 것이다.

```typescript
interface Product {
	id: number;
	name: string;
	price: number;
}

function logProduct(product: Product) {
	const id: number = product.id;
	const name: string = product.name;
	const price: number = product.price;
	console.log(id, name, price)
}
```

위와 같은 코드가 있다고 가정해보자. 그런데 id에 문자도 들어갈 수 있다는 사실을 깨닫고 Product 인터페이스의 id 타입을 고치려고 하면 다음의 코드와 같이 logProduct 함수 내의 id 변수 선언에 있는 타입과 맞지 않아 오류가 발생한다.

```typescript
interface Product {
	id: string;
	name: string;
	price: number;
}

function logProduct(product: Product) {
	const id: number = product.id;
	  // ~~ 'string' 형식은 'number' 형식에 할당할 수 없습니다.
	const name: string = product.name;
	const price: number = product.price;
	console.log(id,name,price);
}

```

굳이 logProduct 함수 내에 명시적 타입 구문을 적었기 때문에 이런 에러가 난 것이다. 

추가로, 해당 코드 logProduct는 비구조화 할당문을 사용하여 구현하는게 더 낫다. 비구조화 할당문은 모든 지역변수의 타입이 추론되도록 하기 때문이다. 

### 명시적 타입 구문이 필요한 상황

물론 **정보가 부족해서 타입스크립트가 스스로 타입을 판단하기 어려울 때**는 명시적 타입 구문이 필요하다. 

명시적 타입 구문을 작성해야 할 때는 함수/메서드 시그니처에는 타입 구문을 포함하되, 함수 내에서 생성된 지역 변수에는 타입 구문을 넣지 않고 타입 스크립트 코드를 작성하여 가독성을 높일 수 있다.

(이게 가능한 이유는 타입스크립트에서 변수의 타입은 처음 등장할 때 결정되고 매개변수의 최종 사용처까지 참고하여 타입을 추론하지 않기 때문이다.) 

### 함수 매개변수에 타입구문을 생략하는 경우

- 기본값이 있는 경우 : 기본값이 있을 경우 이를 기반으로 타입 추론이 가능하다.

예시)
```typescript
function parseNumber(str: string, base=10) {
 // ...
}
```

- 일반적인 라이브러리: 타입 정보가 있는 라이브러리에서 콜백 함수의 매개변수 타입은 자동으로 추론된다.

예시) express HTTP 서버 라이브러리 사용시
```typescript
app.get('/health', (request, response)=>{
response.send('OK')
})
// request와 response 매개 변수의 타입 구문은 생략해도 됨.
```

(문맥이 타입 추론을 위해 어떻게 쓰이는지는 아이템 26 참고)

### 타입 추론이 가능함에도 타입을 명시하는 상황

- 객체 리터럴을 정의할 때 : 객체 리터럴을 정의할 때 타입을 명시하면 잉여 속성 체크가 동작한다. 잉여 속성체크의 장점은 아이템 11에서 확인할 수 있다. 또한 변수가 사용되는 순간이 아니라 할당하는 시점에서 오류가 표시되어 더 정확하다.

- 함수의 반환에 타입을 명시해 오류를 방지 : **구현상의 오류가 함수를 호출한 곳까지 영향을 미치지 않도록 하기 위해**서 타입 구문을 명시하는 게 좋다. 반환 타입을 명시해야 하는 이유는 이외에도 두개가 더 있다. 첫째, **반환 타입을 명시해 함수에 대해 더욱 명확하게 알기위해**.  둘째, **명명된 타입을 사용하기 위해**.

### 타입 구문이 정말 필요한지 체크하기

eslint 규칙 중 `no-inferrable-types`를 사용해서 타입 구문이 필요한지 체크할 수 있다.