# Item 20 다른 타입에는 다른 변수 사용하기



일반적으로 변수의 값은 바뀔 수 있지만 타입은 바뀌지 않는다.
따라서 변수의 값이 다른 타입을 가질 수 있는 경우, 오류가 발생한다. 유니온 타입을 사용하여 타입을 확장하면 다른 타입을 포함할 수 있도록 할 수 있지만, 더 많은 문제가 생길 수 있다.
결국 **타입이 다를 때는 별도의 변수를 도입하는 것이 낫다**.

## 별도의 변수를 사용하는 것의 장점

- 서로 관련없는 값을 분리할 수 있음
- 변수명을 더 구체적으로 지을 수 있음
- 타입 추론을 향상시키고 타입 구문이 필요없음
- 타입이 간결해짐
- let 대신 const로 변수를 선언하게 됨. (코드가 간결해지고 타입체커가 타입추론하기에도 더 좋음)

### 주의 사항: 재사용되는 변수와 가려지는 변수

재사용되는 변수와 가려지는 변수를 혼동하지 않아야 한다.

가려지는 변수란 이름만 같고 서로 아무런 관계가 없는 변수다 (스코프가 달라서).

```typescript
const id = "12-34-56";
fetchProduct(id);

{
const id = 123456;
fetchProductBySerialNumber(id);
}


//위의 코드가 이상해서 다른 예시)
let x = 10; 

function foo() {
	let x = 20; console.log(x); 
	} 

foo(); // output: 20

// 여기서 변수 x는 두번 선언됨. 한번은 전역에, 하나는 foo 함수 내에. 둘은 이름은 같지만 실제로는 스코프가 다르므로 아무런 관련이 없고 이를 가려지는 변수라고 한다.
```

그런데 가려지는 변수는 사람들한테 혼동을 주므로 사용을 지양해야 한다.