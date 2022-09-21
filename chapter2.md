
# Chapter 2 
 
Jenkins CI 구성 요소인 Git , Docker에 대해서 자세한 설명과 함께 Docker Compose 에 대해서도 실습을 한다.   



1. GitHub Action와 Workflow 실습하기  

2. Docker compose 설치 및 WordPress, SQL 구성 예제 실습

<br/>

##  Github action 과 workflow 사용하여 도커 이미지 생성 ( Goodbye Jenkins )

<br/>

### GitHub Package

<br/>

github 에서도 Packages 라는 이름으로 도커 레지스트리를 기능을 지원한다.  
현재 private은 500 메가 까지는 제공을 하고 있다.  

docker 이미지 이름은 앞에 ghcr.io가 붙는다.  
github의 본인 계정으로 이동하면 packages tab을 볼수 있다.  

<img src="./assets/github_package.png" style="width: 80%; height: auto;"/>  

<br/>

### GitHub Docker 이미지 빌드 후 github package 에 push 

<br/>

우리가 그동안 Jenkins 를 통하여 Docker Build 를 수행 했지만 이제는
Github의 Action , workflow 기능을 이용하여 빌드를 수행해본다.   

<br/>

https://github.com/shclub/edu1 를 본인의 github에 fork 한다.  

Actions tab 을 클릭한다.  

<img src="./assets/github_action1.png" style="width: 80%; height: auto;"/>  

템플릿 목록이 나오고 먼저 Github package 에  push 하기 위해서 Publish Docker Container Template을 선택한 후 configure 를 클릭한다.  
- IOS 나 Android의 경우는 search 메뉴에서 검색한다.    

<img src="./assets/github_action_template.png" style="width: 80%; height: auto;"/>  

docker-publish.yml 화일이 아래 처럼 생기고 schedule 부분의 2개 라인만
주석 처리하고 Start commit을 클릭한다.  

<img src="./assets/github_action3.png" style="width: 100%; height: auto;"/>  

./github/workflows 폴더가 생성이 되고 docker-publish.yml 화일이 생성 된것을 확인 할수 있다.  

<img src="./assets/github_action4.png" style="width: 100%; height: auto;"/>  

다시 Actions Tab을 클릭한다.  
Docker 라는 workflow 가 생성이되고 오른편에 pipeline 이 실행 되고 있는것을  확인할 수있다.  

<img src="./assets/github_action5.png" style="width: 100%; height: auto;"/>  

정상적으로 수행이되면 파란색으로 아이콘이 변경이되고 에러가 발생하면 붉은색으로 나온다.  

<img src="./assets/github_action6.png" style="width: 100%; height: auto;"/>  

해당 파이프라인 클릭 ( create  Docker-publish.yml ) 하면 빌드 화면으로 넘어가고 Build를 클릭하면 오른편에 파이프라인 세부 로그를 볼 수있다.  

<img src="./assets/github_action7.png" style="width: 100%; height: auto;"/>  

생성된 빌드 이미지를 보기 위해서는 본인 계정을 클릭한다.  

<img src="./assets/github_action8.png" style="width: 100%; height: auto;"/>  

repository에서도 오른편에서 package 를 통해 확인 할 수도 있다.  

<img src="./assets/github_action8-1.png" style="width: 100%; height: auto;"/>  

Packages를 클릭하면 신규로 생성된 도커 이미지를 확인 할 수 있다.  

<img src="./assets/github_action9.png" style="width: 100%; height: auto;"/>  

해당 도커 이미지를 클릭하면 docker pull을 위한 도커 이미지를 명령어를 확인 할 수 있고 오른편에는
package를 설정할수 있다.  

기본 설정은 public 이다.  

<img src="./assets/github_action10.png" style="width: 100%; height: auto;"/>  

생성된 도커를  터미널 창에서 아래와 같이 실행한다.  
기본 tag는 master 이다.  


```bash
root@jakelee:~# docker run ghcr.io/shclub/edu1:master
 * Serving Flask app 'app' (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on all addresses (0.0.0.0)
   WARNING: This is a development server. Do not use it in a production deployment.
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000 (Press CTRL+C to quit)
 ```  

  
<br/>

### GitHub Docker 이미지 빌드 후 docker hub  에 push 

<br/>

GitHub package 가 아닌 Docker hub 에 push 해본다.  

Actions Tab으로 이동하여 New workflow를 클릭한다.  

<img src="./assets/github_action11.png" style="width: 100%; height: auto;"/>  

아래의 내용을 복사한다.    

```bash
name: Publish Docker image

on:
#  release:
#    types: [published]
  push:
    branches: [ master ]
    # Publish semver tags as releases.
#    tags: [ 'v*.*.*' ]
  pull_request:
    branches: [ master ]
    
jobs:
  push_to_registry:
    name: Push Docker image to Docker Hub
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v3
      
      - name: Log in to Docker Hub
        uses: docker/login-action@f054a8b539a109f9f41c372932f1ae047eff08c9
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      
      - name: Extract metadata (tags, labels) for Docker
        id: meta
        uses: docker/metadata-action@98669ae865ea3cffbcbaa878cf57c20bbf1c6c38
        with:
          images:  <본인 도커 계정>/edu1
      
      - name: Build and push Docker image
        uses: docker/build-push-action@ad44023a93711e3deb337508980b4b5e9bcdc5dc
        with:
          context: .
          push: true
          tags:  <본인 도커 계정>/edu1
          #${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```  

아래와 같이 생성이 되면 화일명을 docker-hub-publish.yml로 변경을 하고 image 이름을 원하는 이름으로 변경한다.  
본인 docker hub id 를 사용한다.  

```bash
#before
      - name: Extract metadata (tags, labels) for Docker
        id: meta
        uses: docker/metadata-action@98669ae865ea3cffbcbaa878cf57c20bbf1c6c38
        with:
          images:  <본인 도커 계정>/edu1
      
      - name: Build and push Docker image
        uses: docker/build-push-action@ad44023a93711e3deb337508980b4b5e9bcdc5dc
        with:
          context: .
          push: true
          tags:  <본인 도커 계정>/edu1
          labels: ${{ steps.meta.outputs.labels }}
#after
      - name: Extract metadata (tags, labels) for Docker
        id: meta
        uses: docker/metadata-action@98669ae865ea3cffbcbaa878cf57c20bbf1c6c38
        with:
          images: <본인 도커 계정>/edu1 <-- 수정
      
      - name: Build and push Docker image
        uses: docker/build-push-action@ad44023a93711e3deb337508980b4b5e9bcdc5dc
        with:
          context: .
          push: true
          tags: <본인 도커 계정>/edu1   <-- 수정  
          labels: ${{ steps.meta.outputs.labels }}
```  

<img src="./assets/github_action12.png" style="width: 100%; height: auto;"/>  

start commit 버튼을 클릭하면 화일이 신규로 생긴것을 확인할 수가 있고  빌드가 수행이 된다.  

<img src="./assets/github_action13.png" style="width: 100%; height: auto;"/>  

Actions Tab 으로 이동하면 Publish Docker image 가 생성이 되고 빌드 파이프 라인이 성공 1개 에러 1개가 발생 한 것을 확인 할 수 있다.  

<img src="./assets/github_action14.png" style="width: 100%; height: auto;"/>  

에러를 클릭하면 세부 파이프라인 창으로 이동을 하고 오른편 화면에 에러가 난 곳을 확장 하여 에러메시지를
확인한다.  

에러 메시지는  Github Repository (edu1)에 도커 허브 credential을 만들지 않아서 발생한 에러이다.

<img src="./assets/github_action15.png" style="width: 100%; height: auto;"/>  

도커 허브 Credential을 만들기 위해서 setting -> secret 으로 이동하여 Action을 클릭한다.  

<img src="./assets/github_action_docker1.png" style="width: 100%; height: auto;"/>  

New Repository Secret를 클릭한다.  

<img src="./assets/github_action_docker2.png" style="width: 100%; height: auto;"/>  

docker-hub-publish.yml 에 생성한 것처럼 설정을 한다.   

```bash
with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
```

아래와 같이 secret을 생성한다.  

<img src="./assets/github_action_docker3.png" style="width: 100%; height: auto;"/>   

2개의 secret이 생성된 것을 확인 할수 있다.  

<img src="./assets/github_action_docker4.png" style="width: 100%; height: auto;"/>   

Actions Tab 으로 이동하여 Publish Docker image 를 선택하고 에러난 화면을 클릭하여 세부 파이프라인 창으로 이동한다.  

<img src="./assets/github_action_docker5.png" style="width: 100%; height: auto;"/>  

오른쪽 상단에 Re-run failed job을 선택한다.  

<img src="./assets/github_action_docker6.png" style="width: 100%; height: auto;"/>  

Re-run jobs를 클릭한다.  

<img src="./assets/github_action_docker7.png" style="width: 100%; height: auto;"/>  

다시 파이프라인을 재실행을 한다.  

<img src="./assets/github_action_docker8.png" style="width: 100%; height: auto;"/>  

성공으로 빌드 된것을 확인 할 수 있다.  

<img src="./assets/github_action_docker9.png" style="width: 100%; height: auto;"/>  

도커 허브로 이동하여 생성된 이미지를 확인한다.  

<img src="./assets/github_action_docker10.png" style="width: 100%; height: auto;"/>  


<br/>

### 수동으로 Actions workflow 실행 

<br/>

workflow는 schedule 또는 event trigger를 통해서 동작을 하지만 수동으로 원할때만 빌드 할수 있도록 구성을 할 수 있다. 

<br/>

docker-hub-publish.yml 화일에서 on 아래에 아래와 같이 추가해 준다.  
기존의 값은 주석 처리한다.  ( jobs 아래 내용은 수정하지 않는다 )  

```bash
on:      
  workflow_dispatch:
    inputs:
      name:
        description: "TAG"
        required: true
        default: "master"
#  schedule:
#    - cron: '25 2 * * *'
#  push:
#    branches: [ master ]
#    # Publish semver tags as releases.
#    tags: [ 'v*.*.*' ]
#  pull_request:
#    branches: [ master ]
```  

Commit을 하고 Actions Tab으로 이동하면 아래와 같이 Run workflow 버튼이 생성된 것을 확인 할 수 있다.  

버튼을 클릭하면 설정한 Input 값이 나오고 Run을 하면 실행이 된다.  

<img src="./assets/github_action_manual.png" style="width: 80%; height: auto;"/>  


## Docker Compose 

<br/>

### Docker Compose 개요

Docker compose란, 여러 개의 컨테이너로부터 이루어진 서비스를 구축, 실행하는 순서를 자동으로 하여, 관리를 간단히하는 기능이다.

 Docker compose에서는 compose 파일을 준비하여 커맨드를 1회 실행하는 것으로, 그 파일로부터 설정을 읽어들여 모든 컨테이너 서비스를 실행시키는 것이 가능하다.

### Docker Compose를 사용하기까지의 주요한 단계

Docker compose를 사용하기 위해서는, 크게 나눠 아래의 세 가지 순서로 이루어진다.

1) 각각의 컨테이너의 Dockerfile를 작성한다(기존에 있는 이미지를 사용하는 경우는 불필요).

2) docker-compose.yml를 작성하고, 각각 독립된 컨테이너의 실행 정의를 실시한다(경우에 따라는 구축 정의도 포함).

3) "docker-compose up" 커맨드를 실행하여 docker-compose.yml으로 정의한 컨테이너를 개시한다.

 Docker compose는 start, stop, status, 실행 중의 컨테이너의 로그 출력, 약간의 커맨드의 실행이과 같은 기능도 가지고 있다.

<br/>

### Docker Compose 설치

본인 VM에 터미널로 접속하여 아래 명령어를 입력한다.

```bash
apt-get update && apt install docker-compose
```  
중간에 추가 설치 내용이 나오면 Y를 입력하고 엔터를 친다.

<img src="./assets/docker_compose_install.png" style="width: 60%; height: auto;">

도커 컴포즈 버전을 확인하고 아래와 같이 나오면 정상적으로 설치가 된 것이다.

```bash
docker-compose --version
```  
<img src="./assets/docker_compose_version.png" style="width: 60%; height: auto;">  

### Docker Compose yaml  

도커 실행 명령어를 yml 파일로 스크립트 문서화 하여 관리한다.  

이번 예제는 워드프레스(WordPress)라는 오픈 소스 블로그 소프트웨어 이고
워드 프레스를 통해서 PHP기반의 웹페이지를 만들수 있다.
기본적으로 Mysql DB를 내장하고 있다.


터미널로 접속하여 새로운 폴더를 하나 생성하고 생성된 폴더로 이동한다.
vi 에디터로  docker-compose.yml 를 생성한다.

```bash
mkdir edu2-1
cd edu2-1
vi docker-compose.yml
```  

<img src="./assets/docker_compose_mkdir.png" style="width: 60%; height: auto;"> 

아래의 내용을 복사하여 docker-compose.yml에 붙여 넣기한다.
esc 눌러주고 :wq를 입력하여 저장하고 나온다.


```yaml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - ./mysql:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: wordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress
   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "40004:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306 // mysql 기본 설정
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
     volumes:
       - ./wp:/var/www/html
```  

맥북 M1 사용자는 M1용 mysql 도커 이미지가 없어 아래의 샘플로 테스트 한다.  


```yaml
version: '3.3'
services:
    frontend:
        image: ghcr.io/shclub/edu12-3:master
        depends_on:
          - backend
        ports:
          - 80:80
        environment:
          REACT_APP_API_URL: http://backend:8080
    backend:
        depends_on:
          - db
        ports:
          - 8111:8080
        image: ghcr.io/shclub/edu12-4:master
        environment:
          # dev is H2 DB , prd is MariaDB
          SPRING_PROFILES_ACTIVE: prd
          SPRING_DATASOURCE_USERNAME: edu
          SPRING_DATASOURCE_PASSWORD: caravan
    db:
        image: ghcr.io/shclub/mariadb:10.6.9-debian-11-r4
        ports:
          - 3306:3306
        volumes:
            - ${PWD}/database:/var/lib/mysql
            - ${PWD}/schema.sql:/docker-entrypoint-initdb.d/init.sql
        environment:
          MARIADB_ROOT_PASSWORD: New1234!
          MARIADB_DATABASE: edu
          MARIADB_USER: edu
          MARIADB_PASSWORD: caravan
```  
<br/>

Docker compose 명령어를 사용하여 컨테이너를 실행한다.

```bash
docker-compose up -d
```  

- 현재 디렉토리에 있는 docker-compose 파일을 실행하여, docker-compose 파일에 정의된 컨테이너를 실행한다.
- -d 옵션을 주면 detached(백그라운드)으로 실행한다.
- –force-recreate 옵션으로 컨테이너를 새로 만들 수 있다.
- –build 옵션으로 도커 이미지를 다시 빌드한다. (build로 선언했을 때만)  


<img src="./assets/docker_compose_up.png" style="width: 60%; height: auto;"> 

Docker ps를 해보면 2개의 컨테이너가 떠 있는 것을 확인 할 수 있다.

```bash
docker ps 또는 docker-compose ps
```  

<img src="./assets/docker_compose_ps.png" style="width: 60%; height: auto;"> 


해당 폴더를 보면 .wp 와  .mysql 폴더가 생성 된 것을 확인 할수 있다.

```bash
ls
```  
<img src="./assets/docker_compose_ls.png" style="width: 60%; height: auto;">

docker compose 기동시에 volumes 설정이 로컬 폴더와 컨테이너 폴더를 연결 한것 을 볼수 있다. 

<img src="./assets/docker_compose_volume.png" style="width: 60%; height: auto;">

브라우저를 통해서 http://localhost:40004로 접속하여 정상적으로 로드 된걸 확인 할 수 있다.

도커 컴포즈 명령어
- 컨테이너 종료 : docker-compose down
- 컨테이너 정지 : docker-compose stop  
- 컨테이너 로그 보기 : docker-compose logs
- 컨테이저 재시작 : docker-compose restart
- stop으로 정지 된 컨테이너 시작 : docker-compose start
- 도커 네트웍 지우기 : docker network prune

<img src="./assets/docker_compose_web.png" style="width: 60%; height: auto;">  

다른 docker-compose를 테스트 하기 위해서는 폴더를 새로 만든 후 docker-compose.yaml를 만들고 해당 폴더에서 명령어를 수행한다.

<br/>

## 과제

<br/>

### 과제 1


 docker compose로 구성한 mysql container에 접속하여 로그인 한 후 wordpress db에 customer 테이블을 생성해 본다.  

<br/>

### 과제 2

mysql container 에 접속하여 로그인 한 후 wordpress db 에 
아래 테이블 script를  host에 저장된 화일을 사용하여 test 테이블을 생성해 본다.  

  https://github.com/shclub/edu1/blob/master/test.sql 화일을 다운 받는다.  

   
  - TIP : 화일 이동 방법은 cp 명령어 사용.  

    호스트 -> 컨테이너
    ```bash
    docker cp [host 파일경로] [container name]:[container 내부 경로]
    ```
    컨테이너 -> 호스트  

    ```bash
    docker cp [container name]:[container 내부 경로] [host 파일경로]
    ```  

<br/>

### 과제 3

docker 컨테이너 GUI 관리 툴인 portainer를 설치하고 웹에서 접속하여
          모니터링한다.
  - url  참고 :  https://docs.portainer.io/v/ce-2.11/start/install/server/docker/linux
  - 웹 포트는 40005로 expose 한다 ( https 9443 포트 변경 필요 ).
  - 웹브라우저 접속은 https://(본인VM Public IP):40005  
     admin 비밀번호 신규로 생성 (8자리 이상) 한다.