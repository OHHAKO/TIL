# JavaScript

## 20/08/01 Trouble Shooting

- 팀원이 보내준 nodejs 웹 크롤링 프로그램을 node에서 실행시켰고 에러가 발생했다.
- 사용중이던 노드 버전은 10.15.1 , npm 버전은 6.4.1 이였다. `fs` 등 라이브러리를 import 하려고 했는데 `SyntaxError: Unexpected identifier`가 발생했다. import는 ES6에서 사용하는 구문이므로 이를 require로 바꾸었더니 잘 동작되었다.

- `(node:4516) UnhandledPromiseRejectionWarning: Error: Err 발생 : function () { [native code] } at Promise (C:\Users\khk05\OneDrive\바탕 화면\git\data class\index.js:24:20) at new Promise (<anonymous>) at delay (C:\Users\khk05\OneDrive\바탕 화면\git\data class\index.js:23:3) at main (C:\Users\khk05\OneDrive\바탕 화면\git\data class\index.js:41:9) (node:4516) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). (rejection id: 2) (node:4516) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.` 에러가 발생했다.

- promise 사용 부분에 then 매개변수로 err를 받는 처리를 추가했더니 에러 메세지가 전과 상이해졌다. `(node:16628) UnhandledPromiseRejectionWarning: Error: Err 발생 : undefined at Promise.then (C:\Users\khk05\OneDrive\바탕 화면\git\data class\index.js:26:11) (node:16628) UnhandledPromiseRejectionWarning: Unhandled promise rejection.`
  `This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). (rejection id: 1) (node:16628)`
  `[DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.` 이는 catch로 에러를 처리했더니 해결되었다.

- 그리고 크롤러 애플리케이션 동작이 수행되더니 '모든 페이지를 방문해 판결문 아이디(sid)를 수집한 이후 각 전문 링크에 방문해 전문을 수집하는 과정'에서 또 다른 에러가 발생했다.

  - `(node:1096) UnhandledPromiseRejectionWarning: Error: Error: failed to find element matching selector "#container .title_area h2" at Frame._$evalExpression (C:\Users\khk05\OneDrive\바탕 화면\git\data class\node_modules\playwright\lib\frames.js:417:19)`

  - `-- ASYNC -- at Frame.$eval (C:\Users\khk05\OneDrive\바탕 화면\git\data class\node_modules\playwright\lib\helper.js:78:23) at _attributeToPage (C:\Users\khk05\OneDrive\바탕 화면\git\data class\node_modules\playwright\lib\page.js:145:61) at Page._attributeToPage (C:\Users\khk05\OneDrive\바탕 화면\git\data class\node_modules\playwright\lib\page.js:356:20) at Page.$eval (C:\Users\khk05\OneDrive\바탕 화면\git\data class\node_modules\playwright\lib\page.js:145:21) at Page.$eval (C:\Users\khk05\OneDrive\바탕 화면\git\data class\node_modules\playwright\lib\helper.js:79:31) at main (C:\Users\khk05\OneDrive\바탕 화면\git\data class\index.js:131:30) (node:1096)`

  - `UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). (rejection id: 1) (node:1096) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.`

  - 최종적으로 라이브러리에서 에러가 발생했따는 건...버전이 맞지 않다는 의미이다. 라이브러리처럼 외부에서 받은 코드는 직접 고치지 않는게 좋다. 정말 라이브러리 문제가 확실하다면 해당 라이브러리에 PR을 보내는 식으로 해결하는게 좋다. 그 순서란 1) 해당 라이브러리를 fork해서 고친다음 원래 라이브 대 신 fork해서 고친 내 라이브러리를 다시 설치하는 방법 2)더 간단한 임시방편으로 땜빵을 하려면 몽키패치를 해서 동적으로 코드를 바꿔치기 하는 방법. (JS나 파이썬 같은 코드는 몽키패치 하기가 편하다)
