# Angular Study Progress

| date | Description                        |
| ---- | ---------------------------------- |
| 5.11 | Angular 프로젝트 구성요소 생성     |
| 5.12 | Angular 프로젝트 구성요소, webpack |

## 프로젝트 구성요소

모두 typescript로 작성된 구성요소다. 그러나 모두 필수 요소인가? 각각의 역할을 공부해보자.

- 컴포넌트: 앱 화면을 구성하는 view를 생성하고 관리
- 디렉티브(지시자): 앱 전역에서 사용하는 공통 관심사를 컴포넌트에서 분리해 구현한 것. DOM의 모든 것을 관리하기 위한 지시.(html 속성처럼 태그사용)
  - Angular가 3가지 유형 제공: component 디렉티브/ attribute 디렉티브/ structural 디렉티브
- 파이프: 템플릿 내에서 원하는 형식으로 데이터를 변환하여 표시하는 기능
- 서비스: 컴포넌트의 관심사인 뷰 이외에 부가적 기능(로깅, 서버 통신 등)을 분리한 구현. 애플리케이션 전역의 관심사로 분리하여 의존성 주입이 가능한 클래스로 작성.
- 모듈: 모듈 선택적 사용
- 가드
- 클래스: 클래스
- 인터페이스
- Enum

### 컴포넌트 VS 디렉티브

| 기능                 | Component       | Directive                    |
| -------------------- | --------------- | ---------------------------- |
| 관심사               | 뷰단위          | DOM 요소의 공통기능          |
| 다른 컴포넌트와 관계 | 직접적 관심없음 | DOM 요소의 공통기능          |
| 자식 컴포넌트 유무   | O               | X(컴포넌트에 의해 사용될 뿐) |

### 프로젝트 빌드

빌드가 완료되면 프로젝트 root에 dist 폴더(빌드 결과물 포함)가 생성된다.

```typescript
ng build
```

### 트랜스파이링과 의존 모듈 번들링

- ts를 js로 변환해야 한다.
- 프로젝트가 의존하는 모듈을 load하는 html의 script 태그를 작성해야 한다. (순서 지키며 수작업)

Angular CLI가 해결을 담당한다.

- npm이 프로젝트 생성시 의존 모듈을 자동설치(1,000개 가량)
- 빌드 기능은 의존성 관리를 자동화 하여 진행한다.
  - 빌드 기능은 내부적으로 모듈 번들러인 **webpack**을 사용해 아래와 같은 작업 자동화를 지원한다.
  - ts를 js로 트랜스파일링
    - 사실 컴포넌트 템플릿도 컴파일이 필요하지만 빌드 시가 아닌 **런타임 JustInTime 에 컴파일**된다.
  - 디버깅 용도의 소스맵 파일 생성
  - 의존 모듈과 html,css,js 번들링
  - AoT 컴파일 : Ahead-Of-Time 컴파일이란 프로젝트 빌드시 템플릿을 미리 컴파일하는 작업 (번들링전 템플릿 컴파일)
  - 코드 문법 체크
  - 코드의 규약 준수 여부 체크
  - 불필요한 코드 삭제 및 압축

* build 이전 (src/index.html)

```html
<body>
  <app-root></app-root>
</body>
```

- build 이후 (dist/index.html)
- 번들링된 js파일 로드하는 태그 5개가 추가된다.

```html
<body>
  <app-root></app-root>

  <script type="text/js" src="runtime.js"></script>
  <script type="text/js" src="polyfills.js"></script>
  <script type="text/js" src="styles.js"></script>
  <script type="text/js" src="vendor.js"></script>
  <script type="text/js" src="main.js"></script>
</body>
```
