### Docker

도커를 빌드하면 이미지 컨테이너가 생성된다. 이때 Dokerfile이 있는 디렉터리 루트에서 빌드를 실행한다. Dockerfile에 명시한 설정에 기반하여 이미지가 빌드된다. 

NginX의 서빙 포트는 기본적으로 80번이나 cloud run 컨테이너는 기본적으로 8080을 Listen하고 있으므로 추가적인 포트 설정이  필요하다.

- 컨테이너에서 nginX serving port를 8080 listen 하도록 설정
- 도커 실행시 호스트 8080을 이미지 컨테이너 8080으로 연결
- EXPOSE 8080 (접근할 수 있도록 8080을 여는 작업)


### Docker 명령어

```bash
# 현재 디렉터리(.)에서 이미지 빌드하기. tag는 test
docker build -t test .
# test 태그의 이미지를 실행하기. 컨테이너 이름(--name)도 test로 지정. 호스트 8080을 컨테이너 8080으로 연결
docker run -p 8080:8080 --name test test
# test라는 이름의 컨테이너 종료
docker stop test
# test라는 이름의 컨테이너 삭제
docker rm test
# test라는 컨테이너에 인터렉티브하게 터미널로(it) 접속하면서 sh라는 쉘 스크립트를 실행
docker exec -it test sh
```



### Dockerfile

multi-stage builds: 도커파일에서 `FROM` 상태를 여러개 쓸 수 있다. 각 `FROM` 명령은 다른 base를 쓸 수 있으며 각각이 새로운 build stage를 시작한다. 인공물을 한 stage에서 다른 stage에 선택적으로 카피할 수 있다. 마지막 `FROM` 명령어는 최종 도커 이미지를 생성하고 다르이미지들은 중간의 이미들이다. (최종 도커 이미지가 생성되진 않지만 모든 layers가 캐시된다는 의미)



`FROM` 문법은 `AS`라는 키워드를 지원하는데 이는 현재  이미지에게 논리적 이름을 주어 레퍼런스 변수로 이용할 수 있다. 중간 이미지로부터 파일을 복사하기 위해 `COPY IMAGE_AS_NAME | IMAGE_NUMBER`를 사용한다. (IMAGE+NUMBER는 0부터 시작)



```dockerfile
# node 기반 중간 이미지 생성하며 변수 builder로 이용
FROM node AS builder
# 중간 이미지의 현재 워킹 디렉터리를 app으로 설정 (없으면 생성?)
WORKDIR /app
# git-repo 앱을 현재 워킹 디렉터리 app의 하위에 카피?
COPY . .
# 앱 종속성 설치, 빌드, export 
RUN npm i && npm run build && npm run export

# 소용량 alpine nginx 기반 이미지 생성
FROM nginx:alpine
# htpasswd 이용을 위한 패키지 설치
RUN apk add apache2-utils
# 필요하면 parent 디렉까지 모두 생성 & passwdfile 생성 및 username, pwd 설정 
RUN mkdir -p /etc/apache2/ && htpasswd -b -c /etc/apache2/.htpasswd admin test123
# 중간 이미지의 out 디렉터리를 최종 이미지의 nginx 서빙 디렉터리에 복사 (폴더-폴더이므로 )
COPY --from=builder /app/out /usr/share/nginx/html
# git-repo 루트에 위치한 nginx.conf 파일을 이미지의 default.conf에 복사 (파일-파일이므로 내용복사)
COPY nginx.conf /etc/nginx/conf.d/default.conf
# 외부에서 호스트 8080에 접근 가능하도록 설정
EXPOSE 8080
```



로컬에서 실행하기

- 로컬에서 Dockerfile이 위치한 루트에서 위 명령어를 실행할 경우 Dockerfile을 기반으로 도커가 실행된다.



github remote 저장소에서 실행하기

- 도커를 cloud run 컨테이너에서 실행해야 한다. 



#### Trouble shooting

위 파일을 사용했을때 에러 발생. 이유를 분석해보자!



### dockerignore





### Local docker testing을 위한 PowerShell

본좌가 사용중인 노트북은 windows 운영체제 기반이다. 도커는 linux에서 실행시킬 수 있는데 로컬에서 도커를 테스팅하기 위해 Linux를 위한 Subsystem을 활성화 했다. 바로 이 파워쉘을 이용해서 말이다. 윈도우에서 dokcer가 동작하는 서브시스템의 계층은 아래와 같다.

- container service

- docker

- linux

- WSL2

참고로 WSL 2 설치하고 그 안에서 실행되는 Linux에서 npm i를 했을때랑, 그냥 윈도 powershell이나 윈도 cmd나 윈도 git bash에서 npm i를 했을 때랑, node_modules에 설치되는 내용이 달라질 수 있으므로 유의해야 함

## Cloudbuild yaml

```yaml
steps:
- name: 'gcr.io/cloud-builders/docker'
  args: ['build', '-t', 'gcr.io/$PROJECT_ID/web:$COMMIT_SHA', '.']
- name: 'gcr.io/cloud-builders/docker'
  args: ['push', 'gcr.io/$PROJECT_ID/web:$COMMIT_SHA']
- name: 'gcr.io/google.com/cloudsdktool/cloud-sdk'
  entrypoint: gcloud
  args:
  - 'run'
  - 'deploy'
  - 'kt-cloud-front'
  - '--image'
  - 'gcr.io/$PROJECT_ID/web:$COMMIT_SHA'
  - '--region'
  - 'asia-northeast3'
  - '--platform'
  - 'managed'
  - "--allow-unauthenticated"
  - "--service-account"
  - "713134822564-compute@developer.gserviceaccount.com"
  - "--memory"
  - "128M"
timeout: 1500s
images:
- gcr.io/$PROJECT_ID/web:$COMMIT_SHA


```

**로컬에서 cloud run 테스팅**

- cloud sdk shell로 cloud run 명령어 수행 (**build, deploy**)

- **build**: 로컬의 dockerfile 위치에서 빌드하면 결과가 cloud platform에서 해당 project의 registry에 push 됨

- **deploy**: 명령 수행시 해당 project의 최근 registry의 revision이 배포됨

- 정상 수행시 결과로 service url(호스팅 페이지)를 알려줌


**원격에서 cloud run 테스팅**

-  cloud build 대시보드에서 git repo-cloud project(PROJECT_ID) 연동
-  master브랜치가 트리거 되면 cloud build가 감지
-  cloud build가 해당 repo의 Dockerfile 기반으로 **docker build, docker deploy**를 실행함
-  **build**: 빌드하면 최종 생성되는 이미지가 cloud platform에서 해당 project의 registry에 push 됨
-  **deploy**: 명령 수행시 해당 project의 최근 registry의 revision이 배포됨. gcp의 cloudr run service에서 서비스 단위의 배포를 확인할 수 있음


**원격에서 cloud run 테스팅**

- cloud build 대시보드에서 git repo-cloud project(PROJECT_ID) 연동
- master브랜치가 트리거 되면 cloud build가 감지
- cloudbuild.yaml 설정으로 cloud build 수행 (tagging, 메모리, IAM 정책 확인, 리전)
- cloud build가 해당 repo의 Dockerfile 기반으로 **docker build, docker deploy**를 실행함
- **build**: 빌드하면 최종 생성되는 이미지가 cloud platform에서 해당 project의 registry에 push 됨
- deploy: 명령 수행시 해당 project의 최근 registry의 image container가 배포됨. gcp의 cloudr run service에서 서비스 단위의 배포를 확인할 수 있음

제가 이해한 바는 위와 같다. 여기서 궁금한 점이 두 가지 있다.

1. 로컬에서 직접 수행하던 도커 명령을 cloudbuild.yaml에서 설정할 수 있나보다. 그럼 호스트 8080과 이미지의 8080을 연결하는 부분은 어디서 수행할까?

Dockerfile에 EXPOSE 8080을 설정했기 때문에 8080 port를 여는 동시에 cloud run이 (우리몰래) 따로 `docker run -p 8080:8080`이랑 비슷한 무언가를 해준다.


2. 로컬에서는 powerShell을 이용해 로컬 도커에 직접 접속할 수 있었는데, 원격 도커도 shell을 이용해 직접 접근할 수 있을까?

원래는 원격 도커 컨테이너에도 접근이 가능하지만 Cloud Run 환경에서는 안된다고 한다.


3. git repository를 cloud platform의 프로젝트와 연동시켰으니 cloud run이 git repo를 run/deploy 한것?
yes. cloud build에서 git repo와 cloud platform project를 연동시킴. 

4. cloud run에서 이미지를 deploy 할땐 cloud sdk shell을 이용한건가!?

cloud Run이 Dockerfile을 읽고 명령어를 수행하면 Cloud Run에서 이미지 컨테이너가 실행된다.




---
레퍼런스

https://cloud.google.com/cloud-build/docs/deploying-builds/deploy-cloud-run

Cloud build logs: Cloud Build-History (https://console.cloud.google.com/cloud-build/builds/209578c5-6e41-4cd3-9c4b-a38d57fc51f0;step=0?project=divine-tempo-296906)

