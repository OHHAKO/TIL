# JxxSxx 유지보수 보고서



## 상태 레포트

- 개발 현황: FTP-simple로 원격 서버에 접속해 파일을 추가하거나 수정하고 있음.
- 개발 툴: Visual Code에 Spring 개발 및 빌드환경 셋팅 (관련 모듈 설치)

- 개발 환경 (문서참조)

  -  FTP 서버 OS: linux
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
6.  maven 설치
7. 관련 종속성 설치

## 설치 목록
- [ ] jdk 1.8
- [ ] spring framework 5.0
- [ ] apache 2.4
- [ ] Tomcat 9.0
- [ ] jquery 3.4.1



---

참고 사이트

- VSCODE 환경 SpringBoot 개발환경  https://parkdream.tistory.com/95
- Oracle jdk 설치  https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

