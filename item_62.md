# 마이그레이션의 완성을 위해 noImplicitAny 설정하기

프로젝트 전체를 .ts로 전환했다면 매우 큰 진척이다.

> 한줄요약 : 그러나 noImplicitAny를 설정하는 것이 마이그레이션의 마지막 단계이다.

ts로 바꾸면서 any로 추론된 많은 변수들의 타입을 제대로 정의하고, 

에러를 제대로 뱉을 수 있도록 만들자.

`strict: true` 의 경우 팀 내 모든 사람이 타입스크립트에 익숙해진 다음에 반영하자 :D