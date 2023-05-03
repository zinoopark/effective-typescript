# 공개 API에 등장하는 모든 타입을 익스포트하기

요약:

공개 메서드에 나온 타입은 죄다 익스포트하자. 어짜피 숨길 수 없으니 익스포트하기 쉽게 만드는 것이 좋다.



라이브러리를 제작하려면 프로젝트 초기에 타입 익스포트부터 작성하자. 

함수의 선언에 이미 타입 정보가 있으면 제대로 익스포트 되고 있는 것이고, 타입정보가 없다면 타입을 명시적으로 작성해야 한다.



## 타입을 추출하는 법



1. Parameters와 ReturnType 제너릭 타입을 사용

```
// 이렇게 타입을 숨겨놨다면

interface SecretName {
 first: string;
 last: string;
}

interface SecretSanta {
	name: SecretName;
	gift: string;
}

export function getGift(name: SecretName, gift: string): SecretSanta {
 //...
}


다음과 같은 방법으로 타입 추출이 쉽게 된다.

type MySanta = ReturnType<typeof getGift>;
type MyName = Parameters<typeof getGift>[0];
```





