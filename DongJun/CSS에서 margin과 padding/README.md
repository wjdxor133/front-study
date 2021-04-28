# CSS에서 margin과 padding

이번 4주차에 할 내용은 CSS에서 margin과 padding에 대해서 설명하려고합니다.

<img src="https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/1/103.gif" width="536px" height="289px" />

간단하게 차이점을 말한다면 Border 기준으로 위로 공간이 생기는 것이 `Margin(외부여백)`, 아래에 공간이 생기는 것이 `Padding(내부여백)` 입니다.

## Margin과 Padding 기본값

```javascript
margin: length | auto | inherit;
```

padding(패딩) 에서 기본값은 auto값만 없다.

length : px, pt, cm, %와 같은 수치값을 입력 시킬 수 있다.
auto : 수평을 기점으로 자동으로 양옆 속성을 맞춰줍니다.
inherit : 부모 요소를 상속받습니다.

## Margin과 Padding 복합속성

예시로 margin이나 padding에서

```javascript
margin : auto; // 수평을 중심으로 중앙 정렬을 도운다
margin : 25px; // 상하좌우 모두 25px만큼 띄어줍니다
margin : 25px 50px; // 상하 25px 좌우 50px
margin : 25px 50px 75px // 상단 25px 좌우 50px 하단 75px
margin : 25px 50px 75px 100px // 상단 25px 좌 50px 하단 75px 우 100px
```

상, 하, 좌, 우로 되어있는 것이 아니라 `시계방향으로 이루어지기 때문`에 주의해야한다

## Padding

padding은 본문을 부풀리는 효과이기 때문에
따로 `box-sizing:border-box`를 주지 않는다면 width값이 padding이 합쳐진 값으로 계산된다.

## 마진 상쇄(Margin-collspsed)

이번 글에서 가장 중요한 부분이 아닐까 싶다.
padding 같은 경우에는

```javascript
p.a {padding : 20px 0} // 20px + 30px = 50px
p.b {padding : 30px 0}
```

두개의 padding이 합쳐진 50px로 사이 간격이 생긴다
하지만

```javascript
p.a {margin : 20px 0} // margin : 30px > 20px ?  '30px' : '';
p.b {margin : 30px 0}
```

margin 같은 경우 padding과 같은 상황이면 두 margin이 겹쳐서 가장 높은 값으로 적용 된다.

이렇든 이러한 margin 겹침 현상을 `마진 상쇄`라고한다.

### 마진 상쇄가 일어나는 3가지 상황

1. 인접 형제 박스 간 상하 마진이 겹칠 때
   겹쳐진 두 마진 값을 비교해, 더 큰 마진 값으로 상쇄해 렌더링합니다. 만약 겹쳐진 두 값이 동일할 경우, 중복을 상쇄해 렌더링합니다.
   <img src="https://media.vlpt.us/post-images/raram2/97e16a40-121f-11ea-aaba-65695302c179/01-margin-collapsing-sibling-case.png" width="768px" height="434px" />

2. 빈 요소의 상하 마진이 겹칠 때
   '빈 요소' 란 높이(height)가 0인 상태의 블록 요소를 말합니다.

- height / min-height / padding / border 등 상하로 늘어나는 프로퍼티 값을 명시적으로 주지 않았거나
- 내부에 Inline 콘텐츠가 존재하지 않는 요소
  이 경우 위와 아래를 가르는 경계가 없으므로, 자신의 상단 마진의 값과 하단 마진의 값을 비교해 더 큰 값으로 상쇄합니다.
  만약 겹쳐진 두 값이 동일할 경우, 중복을 상쇄합니다. 특히 빈 요소와 인접 박스들 간에 마진 겹침이 일어나는 구조에서는
  다음과 같이 상쇄가 여러 번 발생하게 됩니다.

<img src="https://media.vlpt.us/post-images/raram2/ffac75c0-121f-11ea-aaba-65695302c179/02-margin-collapsing-emptybox-case.png" width="768px" height="434px" />

`"그럼 빈 요소를 안 만들면 되지 않나?"`라고 생각하실지도 모르겠습니다.
하지만 마크업을 진행하다 보면 생각보다 많은 경우에 빈 요소를 만들어놓게 됩니다.

- 빠른 레이아웃 구성을 위해 div로 영역을 만들어 놓을 경우
- 내부에 요소를 append 하기 위해 빈 컨테이너를 만들어 놓을 경우 등
  height, padding, border 등 높이와 관련된 속성들은 상위로부터 상속되지 않기 때문에,
  위의 경우들을 위해서라도 꼭 인지해야 할 부분이라 생각합니다.

3. 부모 박스와 첫 번째(마지막) 자식 박스의 상단(하단) 마진이 겹칠 때
   마진이란 콘텐츠 간의 간격이고, 간격을 벌리기 위해서는 경계를 필요로 합니다.
   브라우저는 부모 박스와 첫 번째(마지막) 자식 박스 간의 경계를 그 사이에 있는 border / padding / inline 콘텐츠 유무로 판단합니다.
   앞에 설명했던 빈 박스의 마진 상쇄 현상과는 조금 다르게, 이미 명시적으로 height / min-height 값을 줬더라도 이번 경우에선 신경 쓰지 않습니다.

따라서 부모와 첫 번째(마지막) 자식 사이에 inline 콘텐츠(텍스트 등)가 없거나,
상단(하단)에 명시적으로 padding 또는 border 값을 주지 않았다면 마진이 겹치게 됩니다.
`이때, 자식 요소의 마진이 더 크든 작든 상관없이 상쇄된 마진은 부모 박스의 바깥으로만 렌더링이 됩니다.`

3. (1)부모 박스와 첫 번째 자식 박스의 상단 마진이 나란히 겹칠 때
   <img src="https://media.vlpt.us/post-images/raram2/3bc26dc0-1221-11ea-aaba-65695302c179/03-margin-collapsing-firstchild-case1.png" width="768px" height="434px" />
   <img src="https://media.vlpt.us/post-images/raram2/3f05b1e0-1221-11ea-aaba-65695302c179/04-margin-collapsing-firstchild-case2.png" width="768px" height="434px" />
   <img src="https://media.vlpt.us/post-images/raram2/42b57370-1221-11ea-aaba-65695302c179/05-margin-collapsing-firstchild-case3.png" width="768px" height="434px" />

4. (2)부모 박스와 마지막 자식 박스의 하단 마진이 나란히 겹칠 때
   상단 마진끼리 겹칠 때와 같은 원리입니다. 예시 이미지 하나만 보겠습니다.
   <img src="https://media.vlpt.us/post-images/raram2/59ea9cf0-1221-11ea-aaba-65695302c179/06-margin-collapsing-lastchild-case.png" width="768px" height="434px" />

다음과 같이 부모 박스 상단(하단)에 padding 또는 border 값을 주어 벽을 만들어주는 것이 안전합니다.
이렇게 하면 의도했던 대로 첫 번째(마지막) 자식 요소를 배치할 수 있습니다
<img src="https://media.vlpt.us/post-images/raram2/62855f30-1221-11ea-aaba-65695302c179/07-margin-collapsing-recomm-case.png" width="768px" height="434px" />

### 마진 상쇄 규칙 적용

- 마진 상쇄는 인접한 두 박스가 온전한 block-level 요소일 경우에만 적용됩니다.
  (inline, inline-block, table-cell, table-caption 등의 요소는 block-level이 아닙니다.)
- 마진 값이 0이더라도 상쇄 규칙은 적용됩니다.
- 좌우 마진은 겹치더라도 상쇄되지 않습니다.

### 마진 상쇄 규칙 예외

다음과 같은 상황에서는 인접 요소 간 마진 상쇄가 일어나지 않습니다.

- 박스가 position: absolute 된 상태
- 박스가 float: left/right 된 상태 (단, clear 되지 않은 상태)
- 박스가 display: flex 일 때 내부 flexbox item
- 박스가 display: grid 일 때 내부 grid item

참조
https://velog.io/@raram2/CSS-%EB%A7%88%EC%A7%84-%EC%83%81%EC%87%84Margin-collapsing-%EC%9B%90%EB%A6%AC-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4 <br />
