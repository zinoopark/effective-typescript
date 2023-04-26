# 데이터가 아닌, API와 명세를 보고 타입 만들기

> 한줄요약
1. 데이터가 아닌, 명세를 참고해 타입을 생성하는 것이 안정성을 높인다.

잘 설계된 타입은 매우 좋지만, 잘못 설계한 타입은 비극을 초래한다.
이런 양면성이 타입설계를 잘해야한다는 압박을 준다.

만약 타입을 자동으로 생성할 수 있다면 매우 좋을것이다. 어떤 경우가 그러할까?

파일형식, API, specification와 같은 타입은 프로젝트 외부에서 비롯된다. 이러한 경우에 자동으로 타입을 생성할 수 있다.

> 타입 자동생성의 핵심은 예시 데이터가 아니라 명세를 참고해 타입을 생성한다는 점이다. Not 예시 데이터 !!


```javascript
function calculateBoudingBox(f: Feature): BoundingBox | null {
    let box: BoundingBox | null = null;

    const helper = (coords: any[]) => {
        // ...
    }

    const {geometry} = f;
    if (geometry) {
        helper(geometry.coordinates);
        // ~~~~~~~~~~~
        // Property 'coordinates' does not exist on type 'Geometry'
        //   Property 'coordinates' does not exist on type
        //   'GeometryCollection'
    }
    return box;
}
```
여기서 f가 GeometryCollection 타입인 경우, 에러가 난다.

첫번째 해결법은 geometry의 프로퍼티인 type을 체크하는것!
```javascript
const { geometry } = f

if (geometry) {
  if (geometry.type === 'GeometryCollection') {
    throw new Error('GeometryCollections are not supported.')
  }
  helper(geometry.coordinates) // OK
}
```

그러나 그냥 에러를 뱉기보다, f가 GeometryCollection인 경우에도 작동되도록 만들어주는게 좋다.
```javascript
function helper(coordinates: any[]) {}

const geometryHelper = (g: Geometry) => {
  if (geometry.type === 'GeometryCollection') {
    geometry.geometries.forEach(geometryHelper)
  } else {
    helper(geometry.coordinates) // OK
  }
}

const { geometry } = f

if (geometry) {
  geometryHelper(geometry)
}
```

GeoJSON의 타입을 직접 선언한 경우 ,GeometryCollection 같은 예외사항은 포함되지 않았을 것이지만, 명세를 기반으로 하면 사용가능한 모든 값에 대해 작동한다고 확신할 수 있다.

API 호출에서도 비슷하다. API 명세로부터 타입을 생성할 수 있다면 그렇게 하는것이 좋다. 
GraphQL에서 특히 잘 동작한다. 
특정 쿼리에 대한 타입스크립트 타입을 생성할 수 있기 때문.
Apollo와 같은 도구를 사용하면 GraphQL 쿼리를 타입스크립트 타입으로 변환해준다. 


깃헙 오픈소스 라이센스를 조회하는 쿼리의 타입 예시 ( Apollo로 요청에 대한 응답 )
```javascript
export interface getLicense_repository_licenseInfo { //
  __typename: 'License'
  /** Short identifier specified by <https://spdx.org/licenses> */
  spdxId: string | null
  /** The license full name specified by <https://spdx.org/licenses> */
  name: string
}

export interface getLicense_repository {
  __typename: 'Repository'
  /** The description of the repository. */
  description: string | null
  /** The license associated with the repository */
  licenseInfo: getLicense_repository_licenseInfo | null
}

export interface getLicense {  // 쿼리 응답
  /** Lookup a given repository by the owner and repository name. */
  repository: getLicense_repository | null
}

export interface getLicenseVariables { // 쿼리 매개변수
  owner: string
  name: string
}
```

즉 외부 데이터의 경우, 그 사람이 만든 타입을 쓰려고 노력하라!
