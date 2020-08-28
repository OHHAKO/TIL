# JxxSxx 유지보수 보고서

## 상태 레포트

- 개발 현황: FTP-simple로 원격 서버에 접속해 파일을 추가하거나 수정하고 있음.
- 개발 툴: Visual Code에 Spring 개발 및 빌드환경 셋팅 (관련 모듈 설치)

- 개발 환경 (문서참조)

  - FTP 서버 OS: linux
  - legacy/boot/sts 구분 : legacy로 확인
  - Database : mysql
  - 배포: AWS lightsail
  - 테스터: Aws svn (접근불가)

- 시스템구조 (문서참조)

- 필요 작업: 원격 서버의 개발환경과 프로젝트를 on-premise에 그대로 설치한 후 gitlab에서 버전 및 형상관리

## 경험 회고

기존에 내가 알던 Spring의 디렉토리 구조와 프로젝트 루트의 모듈들이 달라 에코시스템을 파악하기가 어려웠다. 내가 전에 경험했던 Spring은 이클립스에서 legacy Spring project를 생성하여 maven으로 종속 모듈을 설치했다. 그런데 FTP로 확인한 프로젝트 폴더는 두개의 프로젝트가 혼합되어 있었는데 다소 복잡한 디렉토리와 생소한 모듈번들로 인해 경로를 열때마다 아찔함을 경험했다.

일단 Visual studio에서 Spring legacy 프로젝트를 생성하여 Spring 관련 모듈을 설치하고 maven으로 종속성 관리를 하고 있었다.

## 프로젝트 이전 방법

1. 로컬에 설치해야 할 목록 확인. (VsCode, jdk, spring, gitlab, tomcat )
   - 추측=> Spring Initializr Java Support, Java Extension Pack
2. 애플리케이션 구조 확인 (jxxSxx 프로젝트 front/server 분리되어 있음)
3. OS 세팅
4. Jdk 1.8 설치 +환경변수 설정
5. spring(boot 경우 tomcat 내장)
   - 추측=> Spring Boot extension pack
6. maven 설치
7. 관련 종속성 설치

## 설치 목록

- [x] jdk 1.8 -> 11
- [x] gitlab
- [x] maven
- [x] Tomcat 9.0 (apache 2.4)
- [ ] <strike> spring framework 5.0 </strike> 무관
- [ ] <strike> mybatis </strike> 무관

참고 사이트

- VSCODE 환경 SpringBoot 개발환경 https://parkdream.tistory.com/95
- Oracle jdk 설치 https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

## 작업일정

1주차

<strike>시스템구조와 팀 작업 파악</strike>

<strike>legacy environment setting in local</strike>

<strike>issue 해결</strike>

<strike>trouble shooting 기록</strike>

devOps 문서화

gitlab gitflow 문서화

<br/><br/>

2주차

issue 해결

trouble shooting 기록
<br/><br/>

3주차

issue 해결

trouble shooting 기록
<br/><br/>

4주차

issue 해결

trouble shooting 기록
<br/><br/>

5주차

issue 해결

trouble shooting 기록
<br/><br/>

## 작업일지 및 Trouble Shooting

7.27

9시반부터 5시반까지 초과근무 한 날. 기술자문 해주시는 분과 하루종일 즐거운 미팅했다. 자바 개발환경을 설치하는데 legacy의 jdk 버전과 VSCode가 요구하는 버전이 맞지 않아 삽질을 좀 한 것 같다.

- 저작권 문제로 OpenJDK 사용해야 함

- VSCode가 최근 자바 개발 시 JDK 11이상을 요구하도록 업데이트 됨

- 삭제후 재설치 할 경우 이전 데이터 확실히 삭제할 것

  7.28
  한 시간 정도 일찍 왔다. 프로젝트를 로컬로 받아오기 위해 aws 서버에 gitlab을 설치했다.
  remote 저장소에서 clone, push 하는데 에러가 발생했다.

clone시 에러 메세지

```
The authenticity of host 'gitlab.com (172.65.251.78)' can't be established.
ECDSA key fingerprint is SHA256:HbW3g8zUjNSksFbqTiUWPWg2Bq1x8xdGUrliXFzSnUw.
Are you sure you want to continue connecting (yes/no)?
```

yes 진행했더니 뜬 에러 메세지

```
Cloning into 'jungle_front'...
The authenticity of host 'gitlab.com (172.65.251.78)' can't be established.
ECDSA key fingerprint is SHA256:HbW3g8zUjNSksFbqTiUWPWg2Bq1x8xdGUrliXFzSnUw.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'gitlab.com,172.65.251.78' (ECDSA) to the list of known hosts.
git@gitlab.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

에러 메세지로 추측되는 정황은 세 가지.

1. 유저의 ssh가 등록되지 않아 컴퓨터에서 접근할 수 없음 (그러기엔 이미 ssh를 등록한 상태였음)
2. 로컬에서 유저정보가 틀려 인식되지 않음
3. 다른 문제다.

username을 변경하고 다시 clone하니 에러 메세지가 줄었다.

```
Cloning into 'jungle_front'...
git@gitlab.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

이 상태에서 clone 대상을 https에서 ssh로 변경하고 다시 clone 했더니 이미 있는 remote라고 떴다.
바로 `git remote -v`로 확인해보니 원격 저장소와 연결이 된 것을 확인했다.

그리고 로컬에서 push하는데 다시 에러 발생.

```
git@gitlab.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

`~/.ssh`에 ssh-keygen을 생성하고 user Setting에서 public key를 등록했더니 잘 동작되었다.
결국 key는 두개가 등록된 셈이다. 다시 push 시도. 그리고 또 발생한 에러메세지.

```
ubuntu@ip-172-26-6-237:~/git/jungle_front$ git push -u origin master
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (6/6), 442 bytes | 442.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0)
remote: GitLab:
remote: A default branch (e.g. master) does not yet exist for stayjungle/jungle_front
remote: Ask a project Owner or Maintainer to create a default branch:
remote:
remote:   https://gitlab.com/stayjungle/jungle_front/-/project_members
remote:
To gitlab.com:stayjungle/jungle_front.git
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'git@gitlab.com:stayjungle/jungle_front.git'
```

저장소 권한 문제였다. master 브랜치를 생성하려면 원격 저장소 관리자에게 요청해 user 권한을 Maintainer or Owner로 바꾸어야 한다.
그리고 push에 성공했다.

### GitLab에서 clone, push가 안될 때

fingerprint, git config global, 저장소 접근권한 문제일 수 있다.

- 저장소를 clone하려면 `ssh-keygen` 생성, gitlab user settings에 ssh 등록을 해야한다.
- 로컬에서 git config name,email을 바르게 썼는지 확인하라
- 저장소에 처음 master 브랜치를 생성할 수 있는 권한은 Maintainer, Owner 에게만 주어진다. (dev 해당없음)

프로젝트에 저장소를 올리려는데 두 가지 웹플리케이션이 각각 front, back로 나뉘어 총 4개의 폴더를 유지하고 있었다.
왜 하나의 웹앱을 front,bak 폴더로 나뉘었는진 잘 모르겠지만.. 원격 저장소에 각각 올려야 할지 묶어 올려야 할지 고민됐다.
front,back 둘다 각 pom.xml를 갖고 있었지만
ide이 자동으로 생성해주는 .project, .settings, .classpath, .springBeans는 back 폴더에만 있어 심히 고민되었다.
계속...

7.29
ide가 자동으로 생성해주는 파일은 프로젝트 이전시 빼야한다는 의견이 있었다. 그래서 .gitignore를 사용해 제외시켰다. 근데 이건 항상 적용되진 않는다. 레가시 프로젝트 환경 조성시 VSCode는 OS와 무관하므로 어떤 환경에서도 원본 IDE 자동생성 파일을 그대로 사용해도 된다.
VSCode는 운영체제와 관련이 없으니 그대로 가져다 쓰면 되므로 .gitignore를 삭제하고 원격 저장소에 올렸다.

back 프로젝트 테스팅 도중 발견한 것들

- tomcat 실행시 에러 발생
- .java에서 getter/setter 인식 안되는 문제 (pom.xml 종속성 문제로 추측)
- VSCode는 OS와 무관하므로 IDE 자동생성 파일 그대로 사용 가능
- 프로젝트 모두 gitlab 저장소에 업로드
- jdk에는 컴파일러가 내장되어 있으며 컴파일 하면 .java가 .class로 변환된다.
- maven으로 빌드하고나면 결과로 war 파일이 산출되는데 .class의 번들이다.

  7.30

* 종속성 문제로 기존 설치를 모두 지우고 다시 설치했다. (openjdk-13_window-x64.zip,apache-maven-3.6.3,apache-tomcat-9.0.36-windows-x64)
* VSCode 설치 확장팩 install(Checkstyle for java,java dpdency viewr,java extension pack, javascript (es6) code snippets, maven for java, tomcat for java)
* front app을 root로 vscode를 실행해야 한다.
* maven필드 아래 프로젝트를 clean,install,package 해서 모두 BUILD SUCCESS 해야 war이 만들어진다.
* target하위에 생성된 ROOT.war를 tomcat에 배포한다.
* Connector와 Server의 port가 달라야 셧다운시 정상종료 된다.

다른pc 환경셋팅 중 발생한 문제

- 설치 및 환경변수 설정이 끝난 후 VSCode에서 tomcat 실행 에러
- 예전처럼 .java에서 getter/setter 인식 실패
- cmd에서 tomcat실행 후 localhost:8080이 잘 떴으나 VSCode의 output에서 에러 발생
- VSCode output 인코딩 실패로 한글이 깨져 선웅님과 trouble shooting 진행이 안됨

  7.31

- pc환경설정 셋팅 완료

- 폴더구조 파악하기
- Owl caraousel 라이브러리 사용법 ([owl-example option사용법 등](https://m.blog.naver.com/PostView.nhn?blogId=blontenote&logNo=30183353169&proxyReferer=https:%2F%2Fwww.google.com%2F) )
- maven에서 clean,install,package 3단계 수동으로 빌드하여 tomcat에 올리는 불편함이 있음. 자동화 시킬 수 있는 방법 필요.
- 혹은 jsp 수정후에 배포되어있는 페이지에 바로 반영시킬 수 있는 방법
- github 사용방법 설명함

  8.3

- React-native 전환/ui,ux 미팅/ 뀨님 미팅
- url에 resource path가 노출되는 문제 해결. url에 요청은 제대로 했는데 도착한 웹페이지의 주소에 .do와 같은 확장자가 붙어있었다. 웹 관리자도구에서 network를 열어보니 ~.do 의 url 주소가 요청되고 있는 것이 아닌가. DNS 서비스,레코드나 web.xml에서 servlet-mapping 문제, welcome-file 등이 문제인 줄 알았는데 `RequestMapping` 문제였다. 전 담당자가 일부러 지금같은 path가 노출되도록 의도한 것 이였고 현재 노출된 path는 실제 resource 이름이 아니였다. 그래서 수정하지 않아도 됐지만 깔끔한 path를 원했으므로 25개의 파일에서 호출된 해당 경로에서(controller의 RequestMapping, return 경로 값) 값을 변경시켰다.

  8.4

- .svn 지우고도 빌드/실행 잘되므로 서버로컬에서 .svn를 삭제하고 명령쉘 스크립트에서 svn명령어 지움
- main페이지 주소에 경로 노출 안보이게 설정. welcome-file에 등록된 index.jsp에서 url 이동없이 main.jsp로 forward 시켰다.
- CSS 미디어쿼리 수정. 타블렛에서도 navbar 보이도록 함(Chrome toggle Device Toolbar)
- gitlab에 PR 보내고 prove, merge 하기

  8.5

### Trouble shooting

에러: 햄버거버튼 클릭시 이외 영역 인터랙션, 스크롤이 비활성화 됨

진단

아 진짜 이상하게 짜놓으셨네. 모달창을 감싸고 있는 gnb_sliding_wrap 클래스 이름을 가진 div가 있는데 활성화시 on 클래스가 붙는다.
이 div의 크기에 따라 모달 슬라이딩 제동거리가 결정된다.근데 이 div는 사실 뷰에서 다른 요소의 위에 붙어있는거라
뷰에서 해당 div의 아래에 있는 요소들을 클릭하면 div가 앞에서 막고 있는 상태다. 어처구니가 없는 상황이다. background를 red로 줬더니 큰 div가 보여서 당황했다.
여기서 내가 고려해야 할 것은... 슬라이딩/ 아래요소 클릭/ 스크롤 활성화 총 3개가 된다.

해결

- .gnb_sliding_wrap의 height를 0px으로, 다른 ui를 방해하지 않도록 사이즈 고정.
- height가 슬라이딩 제동길이로 쓰이는지 슬라이딩이 되지 않았다. 현재는 기능위주의 유지보수가 중요하기 때문에 매니저와 논의하에 효과를 과감하게 지웠다.
- 햄버거 클릭 핸들러에서\$('html').addClass('fixed') 를 주석처리 했다. 추가된 class가 없으므로 html에 'fixed' 클래스를 지우는 코드도(removeClass('fixed')) 주석처리

오늘 버그 수정하면서 깨달은 것은, 남이 짠 코드에서 이슈를 해결하는 과정은 자연스레 아래 순서를 따른다는 것.

남이 짠 코드 이슈 해결과정

- 문제 인식
- 해당부분 코드 분석
- 에러 진단
- 해결방법 찾기
- 코드수정 해결

  8.6

- 기존 사이트 프론트엔드 유지보수
- figma툴 이용해 웹페이지 컴포넌트 설계

  8.7

- 기존 사이트 프론트엔드 유지보수
- 노마드코더에서 react 강의 시청
- react-native 개발환경 설치 (nodejs,npm,expo,expo-client)
- figma툴 이용해 웹페이지 컴포넌트 설계

  8.10

- android 애뮬레이터 설치
- gitlab 웹앱 로컬로 가져와 pc, 안드로이드 애뮬레이터에서 웹브라우저 테스트

  8.11

- 유지보수 CSS 정
- 새로운 웹앱 컴포넌트 UI
- 클라이언트와 기획 미팅

  8.12

- 기술 자문회의 (React와 Redux 세미나)
- wiki에 commit convention 작성

유지보수 알바에서 새 프로젝트 개발자로 본격적으로 투입될 것 같다.
Coding Convention, React 컴포넌트 기반 개발, nextJS, Redux(+Saga), reverse proxy 등 앞으로 공부해야 하는 것들.

8.13

- 개발자 다현님 미팅
- expo 앱 삭제 후 TM에게 받은 프로젝트 간단한 핸들
- 개발환경 셋팅(node,svn 설치)
- nextjs,proxy,redux를 포함한 시스템 구조 파악





8.14

- HOC+Readux 동작 플로우 구조 파악
- NextJS 동적 라우팅 사용
- SD 영입 문제 상의





8.15

- 사이트 접속이 안된다는 클라이언트의 연락이 왔다. 저녁 먹다가 급히 노트북을 켜고 인스턴스에 접속해 다시 배포해봤지만 같은 결과였다. admin은 잘 동작되고 front 사이트가 문제였다.





8.16

SSL, 로드밸런서 포트, DNS, tomcat, 포트 직접접근 설정 및 방화벽 열기 등 여러 접근을 해봤지만 '톰캣'이 문제인 것 같다는 추측밖에 하지 못했다. front는 로그도 출력되지 않는데다 외부 포트 접속 허용을 설정하는 과정에서 블랙스크린밖에 보지 못했기 때문이다. 실시간 대응이 안되는 실력임을 깨닫고 절망감을 느꼈다. 하루종일 시간을 할애해도 결국 해결하지 못하고 서버가 망가졌기 때문에 내 멘탈이 터졌다. 내가 시니어 개발자라면 상황이 달랐겠지. **해본 작업을 나열하자면 이렇다.**

- **SSL 하나가 인증이 안되어서?** 한 인스턴스당 SSL 두개를 가질 수 있는데 우리 경우엔 '사용중인 SSL/비사용 SSL' 두개가 있었다. 비사용 SSL에서 사용 인증이 안되어 경고알람창이 뜬 상태였다. 사용 인증이란 해당 SSL에서 사용하는 도메인을 우리가 실제 다루고 있나?를 검증해야하는 인증이였다. 홈 네트워킹으로 가서 해당 레코드들을 추가해야 검증이 이루어진다. 비사용 SSL의 설정을 확인하니 도메인명이 사용중인 SSL과 똑같았는데 설정이 하나도 안되어 있었기에 필요없다고 판단되어 삭제했다.

- **tomcat이 잘 동작되는가?** 1. startup.sh/ shutdown.sh 를 실행하며 프로세스에 톰캣이 잡히는지 확인했다. 2. Listen 하고있는 port에서 톰캣 포트를 확인했다.
- **외부에서 애플리케이션에 직접 접근하도록 설정** 1. server.xml에서 확인된 커넥터 port를 방화벽에서 INPUT으로 열었다. 2. 라이트세일 사이트 방화벽에서 해당 포트를 열었다. 3. 공인IP로 접근하니 거부되었다. OUTPUT 방화벽도 열어야했을까? shutdown 포트도 열어야 했을까?
- 위 방화벽을 연 상태로 잠시 두었다가 인스턴스에 재접속 해보니 `블랙 스크린`이 발생했다.

알게된것 정리

- 톰캣 서버를 설정하는 파일(server.xml)에서 커넥터 프로토콜이 달랐다. 찾아보니 http,https,ajp가 있는데 ajp 프로토콜은 웹서버에서 받은 요청을 WAS로 전달한다고 한다. 그럼 나머지는?

- 시스템에 상주하는 프로세스 정보는 `$ ps`, 실행중인 모든 프로세스를 리스팅 하려면 `$ ps -ef`를 사용한다. 특정 pid의 상태를 보려면 `$ps -f pid`, 특정 이름이 들어간 프로세스의 상태를 보려면 `$ps -ef |grep java` 특정 프로세스 이름으로 프로세스를 죽이려면 `$ killall -9 PROCESS_NAME` 라는 명령어를 사용한다.

- port들의 상태를 목록으로 확인하려면 `$netstat -tulpen` 한다. 현재 Listen중인 port만 따로 보고싶으면 `netstat -nap | grep LISTEN` 명령어 사용.

  

  

8.17

- https://forums.aws.amazon.com/thread.jspa?threadID=310698 개발자 포럼에서 AWS 우분투 서버 `블랙스크린` 증상이 있는 유저를 찾았고 다른 유저는 Snapshot으로 인스턴스를 새로 만들면 된다는 조언을 했다. 기존 인스턴스로 스냅샷을 만들어 새 인스턴스를 생성해 실행시켜봤지만 여전히 `블랙스크린`밖에 보이지 않았다. 블랙스크린은 악명높은 놈이다. 방법이 없었다.
- 완전히 새로운 인스턴스를 만들어 새로운 개발환경을 생성하는게 낫다는 판단을 했다. 사실상 선택지는 없었음. 애플리케이션을 gitlab에 모셔놨으니 망정이지.



8.18

- 새로운 인스턴스 생성

- 예전 인스턴스 접근 불가한(블랙스크린) 원인 방화벽으로 파악



8.18 화

- front, admin 이용 Tomcat 각각 설치. 두번째 톰캣은 폴더에서 배치파일 직접 접근해 시작/멈춤.
- 각 프로젝트의 war를 tomcat에 배포하고 ubuntu firewall, 인스턴스 방화벽 설정해 직접 ip:port로 접근 성공
- AWS측에 예전 인스턴스에 접근할 수 없는 이슈를 문의했고 답변받기 전 Blackscreen 현상은 방화벽 문제임을 확인함. ssh를 사용하는 port  22번을 열어놓아야 한다.
- dns를 포트에 직접 연결할 것인가, Proxy 엔진을 apache 혹은 nginX로 갈 것인가의 기로에 놓였다.  LB가 고장나지 않았으니 그리고 DNS의 캐시 TTL의 확인이 안되니 기존에 쓰던 LB를 새로운 인스턴스에 그대로 할당하기로 했다.



8.19 수 (재택근무한 날)

- Nginx를 설치하고 Proxy를 설정하여 클라이언트가 브라우저에서 기존 dns로 접속할 수 있도록 했다. 서버 생성은 성공했으나 tomcat이 무너진 이유를 찾지못해 불안하다.

- 인증메일 발송 이슈/ 메인베너 사진 설정/ admin 사진 업로드가 안되는 버그가 발견됐다.
- 인증메일 발송이 개발PC, 인스턴스에서 각각 다르게 돌아감을 확인했다. PC에서는 제대로 동작하나 인스턴스에서는 동작이 안됐다. jsp,javascript를 뜯어봤다. ajax 통신을 하고나면 에러 500을 응답받지만 ajax의 통신 응답은 success로 받는게 좀 이상했다.



8.20 목

- 오전 TM과 시스템 기술 자문 미팅 (db 설계, network, dns, 시스템 아키텍쳐)

- 오후 예비 CTO와 미팅 (협업방식/ 개발 분량 선정 중심의 프로젝트 작업/일정 관리 )

- 새로운 개발자 근무환경 셋팅 및 진행 탑승

- SnapShot으로 테스트 인스턴스 생성. (개발환경이 아예 똑같아야 실제 서버와 같은 환경에서 테스팅이 가능함)

- 인증메일발송 버그 원인 파악 및 bug report 작성

- 다현님과 nextjs-redux 스터디 진행방식 결정
- 로그코드 삽입 후 tomcat log파일 분석

8.21 금
- 이날 오후에 사무실에 CTO님이 오셨다. https 보안에 관한 얘기를 했는데 http에서 https를 요청하는건 괜찮지만 https에서 보안의 위험이 있는 http를 요청하는건 매우 위험한 일이라고 했다. 클라이언트에서 요청한 서버가 의도대로 요청한 서버가 진짜 맞는지를 제3자가 인증하는게 SSL 디지털 인증서라고 한다.

8.24 월
- React Hooks 개념 이해하기 
- fork한 저장소의 브랜치에서 작업하느라 fetch가 밀려있었다. 모두가 origin/master와 동일하도록 fetch 했다.
- legacy 수정하려 했으나 통신 에러 발생으로 다른 기능에서 에러가 발생할까봐 복사해놨던 war를 다시 복구시켰다. 위험했다. 날짜별로 카피떠놓은 나에게 리스펙.. 제일 좋은건 http<->https 통신하도록 설정, 이미지 로딩 안되는 이유 찾는건데 새 개발이 루즈해지는 리스크를 감당할 수 없었다. 개발이 한시가 급한 상황인듯 싶다.

- Commit convention/Github-flow/Code convention를 채택했다.
- 배포전략이란 CI/CD자동화, 커밋규칙 강제화로 개발자는 커밋/푸시만 하고 배포에는 신경쓰지 않도록 하는게 좋다고 한다.

<img src="/img/system1" width="150px" height="80px">

오후에는 CTO님의 자문이 있었다. 개발 로컬에서 빌드/실행 테스트를 하는것도 좋지만 최대한 배포환경과 비슷하게 만드는 것과 더불어 반자동화 시키기 위해 스크립트로 CD를 만들었다. 
고정IP를 할당받는 문제로 인스턴스를 생성하지 않고 기존의 인스턴스에 프로젝트를 올려야 했다. 그래서 준CD가 되었는데 PC에서 깃에 프로젝트를 Push하고 쉘스크립트를 실행하면 인스턴스에서 git을 당겨와 자동으로 빌드/배포를 한다.
이 과정을 수행하기 위해 필요한 작업은 아래와 같다.
- 인스턴스에서 NextJS 실행을 위해 우분투 버전에 부합하는 npm,npx,nodejs를 설치했다.
- PC 터미널에서 명령어로 ssh(Secure Shell)통신해서 Instance에 접근하기 위해 윈도우에서 생성되어 있던 ssh public-key를 인스턴스의 key인식 파일에 추가했다. (pc당 ssh키는 딱 하나만 생성한다고 한다) 그리고 `ssh ubuntu@ip_addr` 명령어로 인스턴스를 실행시킬 수 있었다. 
- **근데 인스턴스가 PC에 private 키가 있는게 맞는거지?** 그렇다. 인스턴스를 접근하려는 기기에서 public을 가져와 인스턴스에 등록시켜야 나중에 개발자가 나갔을때 해당 public 키를 지워 보안을 할 수 있다.

- 이 과정에서 매번 저 명령어를 치는게 까다로우니 음...  `ssh www'라는 명령어만 입력해서 접근할 수 있도록 만들었는데 **이 부분이 기억이 잘 안난다.**
- 인스턴스가 git에 접근하기 위해 ~/.ssh를 만들어 gitlab에 등록하려 했으나 이미 있어서 pass.
- 아 그리고 ubuntu에서 모든 생성과 파일 수정을 ubuntu로 했기때문에 `ls -als`를 써가면서 권한을 확인하고 root만 허용된 상태면 권한을 바꿔주었다.
- 인스턴스를 닫아도 nodeJS를 실행시키는 npm을 동작하게 만들기 위해서 PM2(Process Manager)을 사용했다. 톰캣은 설치시 자동으로 설정되어 있었으나 nodejs는 별도로 다른 라이브러리를 설치해 서버를 실행시킨다. 
- pm2은 설치도 명령어도 좀 까다로운 것 같던데... 아주 유용한 녀석이니 관리 프로세스 생성,삭제,이름 수정정도로 조금 다룰 줄 알면 좋을 것 같다.
- 마지막으로 dns와 nginX 설정. dns에 서브호스트네임을 레코드로 등록해놓고 nginX에서 브라우저로부터 서브호스트네임이 포함된 요청이 들어오면 NextJS 포트, 아니면 다른 설정포트를 요청하도록 했다.

- ubuntu 명령어를 볼 수 있는 사이트를 추천받았는데. 이것도 기억이 잘 안난다.



8.28

**Google Tag Manger 사용하기**

- (V) 1. 기존 GA에 데이터가 잘 들어오는지 확인
- (V) 2. V1에 GTM 스크립트 삽입
- (V) 3. V1의 GA 스크립트를 GTM으로 옮김
- (V) 4. 기존 GA에 데이터가 계속 잘 들어오는지 확인
- (V) 5. V1의 특정 페이지 특정 영역에 V2 컴포넌트 삽입
- (V) 5-1. GTM Trigger 등록  
- (V) 5-2. GTM Tag 등록
- (V) 5-3. 컴포넌트 생성


**로그인 공유 (SSO)**

- (V) 1. V1에서 토큰 생성
- (V) 2. V1에서 프론트로 토큰 전송
- 3. V2에서 토큰 해독
- 4. V2 화면에서 토큰 내용 보여주기

관련 자료
- https://jwt.io/#debugger
- https://tools.ietf.org/html/rfc7519#section-4.1.4
- https://tools.ietf.org/html/rfc7519#section-4.3
- https://blog.udemy.com/java-integer-to-string/?utm_source=adwords&utm_medium=udemyads&utm_campaign=DSA_Catchall_la.EN_cc.ROW&utm_content=deal4584&utm_term=_._ag_88010211481_._ad_437497337004_._kw__._de_c_._dm__._pl__._ti_dsa-449490803887_._li_1009871_._pd__._&matchtype=b&gclid=EAIaIQobChMIy7Dk_8i96wIVy6qWCh2q5ANFEAAYASAAEgKcn_D_BwE
- https://github.com/auth0/java-jwt






