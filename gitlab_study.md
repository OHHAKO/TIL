# GitLab 이해하기

형상관리 도구로 이용되는 GitLab에 대해서 공부해보자.

- GitLab란?

- 타서비스 비교

- Github와 비교

- 동작 방식

- 형상관리 하는 방법

## GitLab란?

GitLab은 기존의 svn과는 다른 분산관리 방식의 형상관리 툴이다. 작업내용을 분리할 수 있고 commit이 중앙 서버에 바로 반영되지 않는다. (svn이란 SubVersion의 줄임말) 설치형 Github라고 이해하면 쉽다. cloud를 제공하지만 on-Premise(로컬설치형)를 기본으로 하는 것 같다. 도입에 가장 중요하게 고려되는 요금 면에서 좋은게 public은 무료 이용이라고 한다.

- Github에서 제공하는 기능 대부분을 제공한다. (코드 병합, 버전 관리, 소스 변화 추적)
- 그럼 무슨 차이냐? `중앙서버의 위치`, `이용 방식`의 차이이다.
- 프로젝트 관리를 위한 자체 CI(Continuous Integration)와 유저인터페이스 제공
- 다른 형상관리 툴 svn에서 github로 이전시키는 지원이 안된다. 새로운 repo 생성해야 한다.

## 타 서비스 비교

- Client/Server 형: SubVersion, Perforce, ClearCase, TFS
  - SVN: 소스코드를 중앙 저장소에 커밋해 다이렉트로 반영한다. 개발자가 version history를 가질 수 없다. local history 기능이 있긴 하나 일시적 기간이라 버전 관리가 안된다.
  - CSV: 저장소 파일명 변경시 제거하여 새로 생성해야 한다. 폴더 이동, 이름 변경을 허용하지 않기 때문이다. ASCII를 지원하지만 유니코드는 제한적이다. 커밋 실패 시 rollback이 안된다.
  - Perforce: 코드 및 바이너리 파일의 변환을 추적한다. 개정 넘버링 인터페이스가 편리하다. 파일명이 바뀌면 히스토리 추적이 힘들다. 특히 유료 서비스임
  - ClearCase
  - TFS
- 분산저장소 형: Git, Mercurial, Bitkeeper, SVK, Darcs
  - github을 다루는데 익숙하면 확장된 버전 gitlab 서비스를 편리하게 이용 가능하다.
- Folder 공유 타입: RCS, SCCS

## Github와 GitLab 비교

위에서 이 두개는 `중앙서버의 위치`, `이용 방식`의 차이라고 언급했다.

github는 프로젝트를 부분적으로 담당해 개발자들이 각자 branch를 만들어 프로젝트를 분산저장 시킨다. Gitlab같은 경우는 설치형 프로그램으로 모든 repo 코드를 개인 서버에 저장시켜 Project를 제어하는 방식이라고 한다. ITPAS팀 첫번째 프로젝트는 정적데이터를 S3에 호출하여 뷰를 렌더링하는 방식의 사이트라 github 형상관리가 문제되지 않았다. Netlify를 이용해 정적 웹 사이트 호스팅과 백엔드 서비스를 제공받을 수 있었기 때문이다. 그러나 JungleStay같은 경우 데이터를 동적으로 호출하고 반영하는 작업을 포함하므로 별도의 server application을 갖고있고 그에 부합하는 형상관리 툴이 필요한 상태이다.

## 동작방식

- CI/CD가 docker에서 실행된다. (CI/CD 지원하는 gitlab runner)
- 연결된 Kubernetes에서 application이 배포된다. (쿠버네티스란 '컨테이너화'된 앱 자동 배포 관리시스템)
- 프로젝트 루트에 .gitlab-ci.yml가 있다면 새로운 push가 들어올때 일련의 pipeline(job bundle)이 실행된다.
  - 테스트: 유닛 테스트, 통합 테스트
  - 린트: 코드 퀄리티 측정, 코드 컨벤션 점검
  - 빌드: 빌드,번들링,Dockerfile 빌드 및 컨테이너 레지스트리 푸시
  - 배포: Helm chart, KNative Funiction, KNative App(컨테이너) 배포

쿠버네티스에서 배포하려면 Application이 컨테이너화가 되어야 하는 것 같다. Gitlab Runner+ windows+ spring 을 연동시키는 모델을 쓰는 케이스를 확인한 바 있다. Spring framework(legacy) 인지 Spring Boot인지에 따라 이용이 좀 다른 듯 하다. Spring boot의 경우 웹 컨테이너(Tomcat)를 내장하고 있고 쉽게 spring app을 만들기 위한 목적으로 설계된 프레임워크 내의 플랫폼. Spring legacy를 이용할 경우 .metadata를 기준으로 git commit 할때 프로젝트 내부에 .gitlab-ci.yml를 추가하면 동작이 안된다. 프로젝트 내부 내용을 root로 끌어와서 작업을 해야한다고 한다.

./metadata/projectname => 이 경우 인식이 안된다고 함

## Gitlab을 통한 형상관리 방법

1. 로컬에 Gitlab 설치
2. 사이트에 프로젝트 저장소 설치후 local로 복제
3. 로컬pc와 원격 저장소 연결.
4. JungleStay 프로젝트에 대해 해결해야 하는 작업 단위로 issue/milestone 등록
5. local에서 해결 및 빌드테스팅 후 PR 푸시
6. matser 작업물을 실제서비스 빌드/배포하는 프로젝트에 어떻게 반영시킬지 상의 필요
