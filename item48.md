# API 주석에 TSDoc 사용하기

> 한줄요약
1. JSDoc을 사용해 명료하게 주석을 달자.

`/** */` , `@param`, `@returns`

```javascript
/**
 *  인사말을 생성합니다.
 *  @param name 인사할 사람의 이름
 *  @param title 그 사람의 칭호
 *  @returns 사람이 보기 좋은 형태의 인사말
 */
function greetFullTSDoc(name: string, title: string) {
  return `Hello ${title} ${name}`;
}
```
![](https://velog.velcdn.com/images/wlsnqslek/post/913c8b00-fd0d-402d-9efd-208a46748f8c/image.png)

타입스크립트에서는 타입 정의에서 JSDoc을 사용할 수 있다.

```javascript
/** 특정 시간과 장소에서 수행된 측정 */
interface Measurement {
  /** 어디에서 측정되었나 */
  position: Vector3D;
  /** 언제 측정되었나? epoch에서부터 초 단위로 */
  time: number;
  /** 측정된 운동량 */
  momentum: Vector3D;
}
```
에디터에서 마우스를 올려보면 필드별 설명을 확인할 수 있다.
![](https://velog.velcdn.com/images/wlsnqslek/post/ea053f4e-b726-4920-9074-c495027e58a5/image.png)