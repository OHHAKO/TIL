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

알아봐야 할 것

- 폴더구조 파악하기
- 라이브러리 사용법 ([owl-example option사용법 등](https://m.blog.naver.com/PostView.nhn?blogId=blontenote&logNo=30183353169&proxyReferer=https:%2F%2Fwww.google.com%2F) )
- maven에서 clean,install,package 3단계 수동으로 빌드하여 tomcat에 올리는 불편함이 있음. 자동화 시킬 수 있는 방법
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


