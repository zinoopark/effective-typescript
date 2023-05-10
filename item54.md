객체를 순회할 때 for-in 루프를 사용하는 다음의 코드의 경우 생긴다. 결론부터 말하자면 객체를 순회할 때 쓰이는 상수k에 추론된 타입이 실제 객체의 타입과 달라서 생기는 문제다.

```
const obj = {
    one: 'uno',
    two: 'dos',
    three: 'tres',
}
for (const k in obj) {
const v = obj[k]
          //~~~~~~ Element implicitly has an 'any' type because expression of type 'string' can't be used to index type '{ one: string; two: string; three: string; }'. No index signature with a parameter of type 'string' was found on type '{ one: string; two: string; three: string; }'.
}
```

따라서 다음과 같이 객체의 타입을 k의 타입에 할당해주면 된다
```
const obj = {
    one: 'uno',
    two: 'dos',
    three: 'tres',
}

let k: keyof typeof obj;

for ( k in obj) {
    const v = obj[k]
}
```

이유는 k에 할당될수 있는 모든 타입을 추론하기 때문에, string으로 추론하게 되는 것. 
그래서 keyof 키워드를 사용해서 타입을 한정시키면 됨. 그러나 그렇게 되면 다음과 같은 문제가 생길 수도 있다.

```

function foo(abc: ABC) {
    let k: keyof ABC;
    for (k in abc) {
        const v = abc[k]};
        }
}
```


여기서 k의 타입은 ABC의 key인데, v의 타입은 string | number 타입으로 추론되어 문제가 생긴다고 한다.

그런데 타입스크립트 플레이그라운드에서 실행해보면 실제로는 에러가 생기지 않는다.

해결책으로 다음과 같이 Objects.entries를 사용하면 된다고 한다.

그런데 실제로는 표준 함수가 아니어서 플레이그라운드에서는 오류를 뿜는다.

```
interface ABC {
    a: string;
    b: string;
    c: number;
}

function foo(abc: ABC) {
for (const [k,v] of Object.entries(abc)) {
	k
	v
	};
}
```