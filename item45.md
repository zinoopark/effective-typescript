Item 45. devDependencies에 typescript와 @typs 추가하기



요약: 

1. 타입스크립트를 devDependencies에 포함시켜야 협업에 좋다
2. @types 의존성은 devDependencies에 포함시켜야 한다. 



npm은 세가지 종류의 의존성을 구분해서 관리한다.

1. dependencies
   - 프로젝트를 npm에 업로드하게 되어 누군가 이것을 다운받게 되면 dependencies에 적힌 라이브러리도 함께 설치된다. 이를 전이 의존성이라 한다.
2. devDependencies
   - 개발할 때만 사용하는 라이브러리, 런타임에는 필요없다. 그래서 npm에 프로젝트를 업로드해도 devDependencies에 적힌 라이브러리는 설치되지 않는다.
3. peerDependencies
   - 런타임에 사용되기는 하지만 의존성을 직접 관리하지 않는 라이브러리들. 제이쿼리처럼 버전이 중하지 않고, 사용자가 선택할 수 있게 한다.



이 중에 자주 쓰이는 것은 dependencies와 devDependencies인데, 타입스크립트와 관련된 라이브러리는 devDependencies에 설치한다.



## 고려해야할 의존성

1. 타입스크립트 그 자체 -> devDependencies
   1. 이유1: 팀원들마다 다른 버전을 사용할 수 있다
   2. dependencies로 설치하면 프로젝트 셋업시 별도의 단계를 추가하게 된다
2. @types -> devDependencies



```
npm install --save-dev @types/react


```





그러나 devDependencies에 넣게 될 때 문제가 생길 수 있는데 이는 item 46에서 다룬다.



