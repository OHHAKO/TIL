# React Study Progress

| date | Description                                          |
| ---- | ---------------------------------------------------- |
| 4.14 | 앱 Github Page로 배포                                |
| 4.15 | lint error 해결, pull request 원격반영 전 로컬테스트 |
| 7.27 | 렌더 성능 최적화, SEO, 페이지백 이슈                 |

# Github Study Progress

- 4/14
  - commit과 pull request 로 보내는 메세지는 컨벤션을 지킨다. (아래 사이트 참고)
  - PR로 보낸 코드에 코멘트를 추가하고 답변을 달면서 code review를 진행했다.
  - 리뷰는 누구나 쓸 수 있는 것 같다만, code review를 지목하려면 PR을 보낸사람이거나 collaborator 초대가 필요하다.
  - 파일의 코드 단위 코멘트는 files changed에서, 큰 맥락은 Conversation에서 진행하는 듯 하다.
  - PR을 merge하기 전, 새로운 pr가 올 경우 동일한 PR에서 누적된다.
  - 등록한 issue는 PR를 보내면서 처리할 수 있다. (이슈 close하기)
- 4/15

  - pull request 원격반영 전 로컬에서 branch생성하고 PR master에 merge시켜 테스트 진행
  - jsx 태그를 사용한다.
  - react용 bootstrap 를 적용했다.
  - component를 작성한다.
- 4/16
  - react(jsx) 문법을 이해하여 component를 다뤄본다.
  - gatsby 환경에서는 GraphQL을 사용해 데이터를 읽어온다: [튜토리얼](https://www.gatsbyjs.org/tutorial/part-four/)
  - prettier(.prettierrc) 설정 이해하기
  - 파일 저장시 prettier설정에 맞게 자동 fix 설정하기
    - `npm run format -- --fix` 를 쓰면 일회성으로 fix해준다.
  - ESLint 설정 이해하기
- 7/27 HSW님과 미팅
  - React에서 CSR 렌더 웹앱은 페이지를 뒤로가기 후 다시 돌아오면 데이터가 날라가 있다. NextJS는 page back 할때 현재뷰의 바로 위에 다른 뷰를 출력시켜 마치 이전 뷰가 사라진 것 처럼 보이게 할때 쓰는 라이브러리? 모듈? 이라고 한다.
  - SEO 이슈. CSR은 DOM이 그려진 이후 데이터가 삽입되기 때문에 검색엔진에서 불리하다. SSR은 JS로 프로그래밍 된 NodeJS기반 서버에서 페이지를 그려서(?) 넘겨주기 때문에 검색엔진에 유리할 수 있다. 
  - React를 SSR 하는 방법 3개. Create-React-App/Gatsby/NextJS. 앞에 두개는 독립적으로 이용하나 NextJS는 Express에 플러그인을 꼽아 사용한다.
  - JS 이슈. Javascript는 엄연히 객체 지향이다. 모든 변수를 Object로 인식하나 처음 변수 선언 시 초기화 타입에 따라 내부적으로 new String()를 호출하며 새로운 메모리를 할당한다. 그러나 새 변수에 그 변수를 저장시키면 새 변수는 새로운 메모리를 할당받지 않은 참조용 변수가 된다. JS가 객체지향인 덕분에 함수의 매개변수로 함수를 받을 수 있다.
  - Redux, Mobx(프레임워크 상태) 는 event를 한번에 감시하게 하는 기능이 있다. 예를들어 여러 input의 데이터를 하나씩 감시할게 아니라 개발자는 redux에 프로그래밍 하여 event에 대해 여러 요소의 데이터를 감시할 수 있다고 한다.
  - 어떤 단위로 컴포넌트를 구성해야 할까? 컴포넌트가 많거나 렌더링 과정이 복잡해질수록 코드를 구현하는데 부담이 커진다. Reuse 가능성을 고려하라. 구현할 페이지가 적다면 컴포넌트를 크게 잡기도 한다. 한마디로 컴포넌트 단위는 상황에 맞게 구성되어야 한다.
  - React에서 css의 스타일을 입히는 방법은 두가지가 있다. 코드가 많으면 CSS In Js로 가면 관리가 편하다. 
  - React-native 이슈. Java의 경우 컴파일을 해야하므로 엔진이 필요하다. JS의 경우 인터프리터, 즉 통역사 역할을 하는 엔진이 내장되어 있어 DOM을 render 한다. 그래서 js코드는 브라우저가 읽고 바로바로 바이너리로 바꾼다. JS를 컴퓨터에서 이해시킬 수 있게 하는 엔진이 NodeJS 다. (Ionic,electron)
  - JS에서도 class를 만들 수 있지만 컴파일 부담이 있기 때문에 function을 사용한다.







### Prettier 설정 이해하기

- endOfLine: EOF 방식. OS별로 처리 방식이 다르다.
- semi:false => 모든 문장 끝에 세미콜론을 넣지 않아도 된다.
- singleQuote:false => default 설정이다. "I'm double quoted" 와 'this "example" is single quoted' 가 허용된다는 의미
- tabWidth:2 => 탭의 스페이스 사이즈. tab 눌렀을 떄 몇칸이 띄어지는시 설정한다.
- trailingComma:es5 => 마지막에 콤마 붙이기 여부를 설정한다.

#### 문제1.

VSCode는 이미 내장된 린트가 있어서 `.setting` 에 들어가면 editor.formatOnsave 설정으로 autofix값을 줄 수 있다. 근데 이 설정을 꺼주는 이유는 eslint설정과 충돌되기 때문이다.

#### 문제2.

eslint가 검사 끝나고 prettier도 대신 실행하게 설정했다. <br>
그런데 npm run format을 실행하면 eslint는 건너뛰고 prettier만 실행하는 것 같다.

#### 해결방안

eslint, prettier 는 cli로 설치를 진행했었다. <br>
서버 실행시 확인이 가능한 에러를 구현단계에서 확인하고 처리하기 위해 VSCode의 확장프로그램을 설치했다.

### ESLint

자바스크립트 문법을 확인해주는 도구다. 포맷팅 기능도 제공한다. <br>
Prettier는 자동으로 코드 스타일을 관리해주는 도구이니 역할이 다르다. <br>
함께 사용하려면 ESLint가 코딩규칙, Prettier가 포맷팅 기능을 한다.

- formatOnSave: 저장할때마다 설정한 코딩 스타일에 따라 자동으로 코드가 변형된다.
- Auto Fix on Save: eslint가 자동으로 고칠 수 있는것들을 자동으로 고쳐준다.
  eslint랑 prettier가 충돌 안나게 설정하는 방법 (확장프로그램 설치경우 말고)

## git message expression

- 구조는 다음과 같을 것입니다.
  - The structure would be like the following
- 차례대로
  - in turn
- 그것은 대부분 추천됩니다.
  - it is recommended most of the time
- 튜토리얼 전반부 재점검
  - Recap of the first half of the tutorial
- QL에 의해 데이터층이 구동됩니다.
  - Data layer is powered by QL
- Hello Gatsby 문구에서 Hello World로 바꾸기
  - Change the “Hello World!” text to “Hello Gatsby!"
- 콘텐츠 변경은 해당 파일을 저장하면 즉시 반영된다.
  - content changes are reflected almost instantly in the browser after you save the file.

---

- [Github 오픈소스 입문을 위한 구체적인 가이드](https://velog.io/@ppp3195/%EC%98%A4%ED%94%88%EC%86%8C%EC%8A%A4-%EC%9E%85%EB%AC%B8%EC%9D%84-%EC%9C%84%ED%95%9C-%EC%95%84%EC%A3%BC-%EA%B5%AC%EC%B2%B4%EC%A0%81%EC%9D%B8-%EA%B0%80%EC%9D%B4%EB%93%9C)
- [pullRequest 공식문서](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/commenting-on-a-pull-request))
- [commit message guide](https://www.conventionalcommits.org/en/v1.0.0-beta.4/)
- [commit message guide2](https://github.com/zeke/semantic-pull-requests)
- [Prettier 공식문서](https://prettier.io/docs/en/options.html)
- [prettier 설명](https://velog.io/@kyusung/eslint-config-4)
