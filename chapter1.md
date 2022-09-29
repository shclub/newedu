
# Chapter 1 

Docker에 대해서 자세한 설명과 함께 Jenkins CI 구성 요소인 Git에 대한 실습을 한다.   

1. Docker 

2. Dockerfile 구성 및 빌드 , Tag , Push 그리고 Swagger 실습 

3. Jenkins를 사용한 CI 구성 하기


## 1. Docker 

<br/>


### Docker 란 ?


<br/>

> 도커 소개 : https://youtu.be/tPjpcsgxgWc

<br/>

소개 및 배경
- 도커는 컨테이너 기반의 오픈소스 가상화 플랫폼이다.
- 다양한 이유로 계속 바뀌는 서버 환경과 개발 환경 문제를 해결하기 위해 등장했다.
   (툴 업데이트, 회사의 툴 사용 변경, 회사의 언어 정책 변경 등 )  
- 기존에 서버와 개발 환경이 변경되면 컴퓨터 셋팅(개발 환경) 등을 다시하거나 
  그 과정에서 발생하는 문제들과 같이, 여러모로 불편한 점이 많았다.
- 도커가 등장하고 서버관리/개발 방식이(컨테이너) 완전히 편리하게 바뀌게 된다.

사용하는 이유
- 도커 허브에 올라온 이미지와 docker-compose.yml의 설정으로 원하는
  프로그램을 편안하게 설치가 가능하다.
- 컨테이너를 생성하여 분리된 환경에 설치하므로 제거도 쉽다.
- 하나의 서버(로컬 호스트)에 포트만 변경하여 동일한 프로그램을 실행하기도 쉽다.
- 도커를 사용하지 않으면 환경변수나 경로 등의 충돌로 피곤하기 그지 없다.

서버관리 방식의 변화
- 전통적인 서버관리 방식은 아래와 같이 각 단계별로 흐름이 있었고, 각 단계가
  업데이트 되거나 어떤 문제가 발생하면 전체 흐름이 중단되는 문제가 있었다.
- 도커 등장 이후 어떠한 프로그램도 컨테이너로 만들 수 있다.
- 서로 다른 프로그램이더라도 컨테이너로 규격화 되었음  
- AWS, Azure, Google cloud 등 어떤 환경에서도 돌아간다.  
  <img src="./assets/docker_overview.png" style="width: 60%; height: auto;"/>  
- 가상머신과 비슷하게 생각할 수 있지만 비슷한점과 다른점이 있다.
- 가상머신처럼 독립적으로 실행되지만, 가상머신보다 빠르고 쉽고 효율적이다.
- 도커는 컴퓨터 자원을 그대로 사용한다. 

도커의 특징
- 도커는 가상머신이 아니고 격리만 해주기 떄문에 성능상 하락이 없다. (성능 하락이 큰 VM과 다르다.)
- 확장성과 이식성
    -  도커가 설치되어 있다면 어디서든 컨테이너를 실행할 수 있다.
    - 오픈 소스이기에 특정 회사나 서비스에 종속적이지 않다.
    - 쉽게 개발서버를 만들 수 있고 테스트 서버 생성도 가능하다.
- 표준성
    - 도커를 사용하지 않는 경우, 각각의 언어로 만든 서비스들의 배포 방식은 모두 다르다.
    - 도커는 컨테이너라는 표준으로 서버를 배포하므로 모든 서비스들의 배포 과정이 동일해진다.
- 이미지
    - 컨테이너를 실행하기 위한 압축파일과 같은 개념이다.
    - 이미지에서 컨테이너를 생성하기 떄문에 반드시 이미지를 만드는 과정이 필요하다.
    - Dockerfile을 이용하여 이미지를 만들고 처음부터 재현 가능하다.
    - 빌드 서버에서 이미지를 만들면 해당 이미지를 이미지 저장소(허브)에 저장하고 운영서버에서 이미지를 불러와 사용한다.
- 설정관리
    - 도커에서 설정은 보통 아래와 같이 환경변수로 제어한다.
    - MYSQL_PASS=password와 같이 컨테이너를 띄울 때 환경변수를 같이 지정한다.
    - 하나의 이미지가 환경변수에 따라 동적으로 설정파일을 생성하도록 만들어져야한다.
- 자원관리
    - 컨테이너는 삭제 후 새로 만들면 모든 데이터가 초기화된다. (제거가 쉽다.)
    - 그러므로 저장이 필요하다면, 업로드 파일을 외부 스토리지와 링크하여 사용하거나 S3같은 별도의 저장소가 필요하다.
    - 세션이나 캐시를 memcached나 redis와 같은 외부로 분리한다.

<br/>

Github 소스를 다운 로드 하기 위하여 Git을 설치 해야 한다.  

<br/>

##  Git  

### Git 개요 

Git이란 소스코드를 효과적으로 관리하기 위해 개발된 '분산형 버전 관리 시스템'입니다. ( 이전 에는 SVN 많이 사용 )  

가장 대중적인 SaaS 형태는 Microsoft에서 제공하는 GitHub 이고
Private 형태로는 Gitlab을 많이 사용 함.  

<br/>

참고 사이트 :  https://backlog.com/git-tutorial/kr/intro/intro1_1.html    

<br/>

### Cloud Shell 용 VM 서버 접속

<br/>

사전에 공유 한 VM 서버 ( OS : Ubuntu ,  CPU : 2core , MEM : 2G )에 접속합니다.  

터미널에서 아래 명령어를 입력하여 로그인 한다.  

로그인 후  connecting 저장 질문이 나오면 yes 를 입력한다. 

```bash
ssh root@(본인 Public ip) 
``` 

<br/>

### Docker 설치

<br/>

> 도커 설치 : https://youtu.be/w8EVLx1_xY0

<br/> 

#### 패키지 인덱스 업데이트

<br/>

```bash
apt-get update
```
<br/>

#### HTTPS를 통해 repository 를 이용하기 위해 package 들을 설치

<br/>

```bash
apt-get -y install  apt-transport-https ca-certificates curl gnupg lsb-release
```
<br/>

#### Docker의 Official GPG Key 를 등록합니다.

<br/>

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

<br/>

#### stable repository를 등록합니다.  

<br/>

```bash
echo \
"deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```  
<br/>

#### Install

<br/>

```bash
apt-get update && apt-get install docker-ce docker-ce-cli containerd.io gnupg2 pass
```


* Ubuntu 에서 도커 로그인 버그가 있어 아래 처럼 에러가 발생하기 때문에 gnupg2 pass 라이브러리를 추가 했음.  

```bash
$ docker login -u shclub -p ******** https://index.docker.io/v1/
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Error saving credentials: error storing credentials - err: exit status 1, out: `Cannot autolaunch D-Bus without X11 $DISPLAY`
```

<br/>

#### 도커 버전을 확인합니다.

<br/>

```bash
docker --version
```
<img src="./assets/docker_version.png" style="width: 60%; height: auto;"/>

<br/>

#### 도커 이미지 다운로드 및 실행하기

<br/>

```bash
docker run hello-world
```
 
<img src="./assets/docker_run_world.png" style="width: 80%; height: auto;"/>


<br/>

### Docker compose 설치.

<br/>

VM 에서 docker compose를 설치 합니다.  

```bash
apt-get update && apt-get install docker-compose
```

<br/>

### openshift console 설치

<br/>

VM 에서 아래와 같이 openshift client를 설치 합니다.  

```bash
root@edu2:~# wget https://github.com/openshift/okd/releases/download/4.7.0-0.okd-2021-09-19-013247/openshift-client-linux-4.7.0-0.okd-2021-09-19-013247.tar.gz
```   

<br/>

tar 화일을 압축을 풉니다.  

```bash
root@edu2:~# ls
cloud-init-setting.sh  openshift-client-linux-4.7.0-0.okd-2021-09-19-013247.tar.gz
root@edu2:~# tar xvfz openshift-client-linux-4.7.0-0.okd-2021-09-19-013247.tar.gz
```  

<br/>

oc 와 kubectl 화일이 생성 된 것을 확인 할수 있습니다.  
oc 는 openshift console 이고 kubectl 은 kubernetes client tool 입니다.  


```bash
root@edu2:~# ls
README.md  cloud-init-setting.sh  kubectl  oc  openshift-client-linux-4.7.0-0.okd-2021-09-19-013247.tar.gz
```  

<br/>

path를 추가 합니다.  

```bash
echo 'export PATH=$PATH:.' >> ~/.bashrc && source ~/.bashrc
```  

<br/>

oc 명령어를 입력해 봅니다.  

```bash
root@edu2:~# oc
OpenShift Client

This client helps you develop, build, deploy, and run your applications on any
OpenShift or Kubernetes cluster. It also includes the administrative
commands for managing a cluster under the 'adm' subcommand.

To familiarize yourself with OpenShift, login to your cluster and try creating a sample application:

    oc login mycluster.mycompany.com
    oc new-project my-example
    oc new-app django-psql-example
    oc logs -f bc/django-psql-example

To see what has been created, run:

    oc status

and get a command shell inside one of the created containers with:

    oc rsh dc/postgresql

To see the list of available toolchains for building applications, run:

    oc new-app -L

Since OpenShift runs on top of Kubernetes, your favorite kubectl commands are also present in oc,
allowing you to quickly switch between development and debugging. You can also run kubectl directly
against any OpenShift cluster using the kubeconfig file created by 'oc login'.

For more on OpenShift, see the documentation at https://docs.openshift.com.

To see the full list of commands supported, run 'oc --help'.
```  

<br/>

VM 에서 접속 테스트를 합니다.  
아이디는 `namespace 이름 - admin` 으로 구성이 되고 namespace 생성시에 자동 생성이 됩니다.  


<br/>

`oc login <API SERVER:포트> -u <아이디> -p <패스워드> --insecure-skip-tls-verify`

<br/>

```bash
root@edu2:~# oc login https://api.211-34-231-81.nip.io:6443 -u edu1-admin -p New1234! --insecure-skip-tls-verify
Login successful.

You have one project on this server: "edu1"

Using project "edu1".
Welcome! See 'oc help' to get started.
```  

<br/>

### Git 설치.

<br/>

본인의 PC에 git을 설치하고 버전을 확인한다.  

```bash
git --version
``` 

<img src="./assets/git_version.png" style="width: 60%; height: auto;"/>

<br/>


### Helm 설치.

<br/>


helm 3.x 이상 버전을 설치한다.

<br/>


```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```  

<br/>


```bash
chmod 700 get_helm.sh
```

```bash
./get_helm.sh
```  

<img src="./assets/helm_install.png" style="width: 80%; height: auto;"/>  

버전을 확인한다.

```bash
helm version
```

<img src="./assets/helm_version.png" style="width: 80%; height: auto;"/>  

helm repository 목록을 조회합니다. 처음 설치 했을때는 아무것도 없습니다.

```bash
helm repo list
```


### Git Clone 하여 github 소스 가져오기.  

github에서  shclub/edu1 를 선택하고 code를 클릭한다.  
https의 url를 복사한다. 오른쪽 복사 아이콘 클릭  

<img src="./assets/github_clone.png" style="width: 60%; height: auto;"/>  

터미털에서 git clone 명령어를 사용하여 로컬에 소스를 가져온다.  
정상적으로 가져오면 edu1 폴더로 이동하여 파일 받아진것 확인한다.

```bash
git clone https://github.com/shclub/edu1.git
```

<img src="./assets/git_clone.png" style="width: 60%; height: auto;"/>  

 
<br/>


### Dockerfile

<br/>

위에서 복사한 Dockerfile은 아래와 같다.

<br/>

Dockerfile 예제

```yaml
# 베이스 이미지 이며 이미지 이름 앞에 아무것도 없으면 docker hub에서 가져온다.
FROM python:3.8-slim

# 컨테이너 안에 작업 폴더를 설정한다.
WORKDIR /app

# 로컬 현대 폴더의 모든 값을 컨테이너 /app 폴더에 복사한다.
ADD . /app

RUN pip install -r requirements.txt

# 기본 이미지는 대부분 GMT+0 기준으로 생성되어 한국 시간으로 변경 해준다
RUN ln -snf /usr/share/zoneinfo/Asia/Seoul /etc/localtime && echo Asia/Seoul > /etc/timezone

# 컨테이너 외부에서 접속할 포트를 적어준다.
EXPOSE 40003

# 컨테이너 로딩시에 사용하는 명령어를 기입한다. 
# EntryPoint와 비슷 하나 CMD를 사용하면 docker run시에 파라미터 설정 가능하다.
CMD ["python", "app.py"]
```

<br/>

<img src="./assets/vi_dockerfile.png" style="width: 60%; height: auto;"/>  

<br/>

vi 에디터는 i (소문자) 를 누른 후  내용을 복사하여 붙여넣기 한다. 
esc 키를 누른 후 :wq를 입력하여 저장하고 나온다.   


app.py 화일의 내용은 아래와 같도 Flask 프레임 웍을 사용하며 내부 포트는 8080 이다.  

app.py    
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == '__main__':
    app.run(host= '0.0.0.0',port=8080,debug=True)
```


<br/>

먼저  아래 명령어를 사용하여 기존의 컨테이너를 종료하고 삭제한다.  

```bash
docker rm -f $(docker ps -aq)
```  

<br/>

이제 아래 명령어를 사용하여 도커 이미지를 생성한다. 
Dockerfile 위치와 같은 폴더에서 실행하여야 하며 생성할 이미지 이름 뒤에 . 을 반드시 사용한다.  


```bash
docker build -t edu1 .
```   

<img src="./assets/docker_build_t1.png" style="width: 60%; height: auto;"/>

Dockerfile의 line by line 으로 단계가 구성되어 도커 레이어를 생성을 한다.
내부 적으로는 docker commit 명령어가 실행이 되면 도커를 재 생성시에는 
Cache를 사용 하기 때문에 훨씬 빨리 빌드가 된다.

<img src="./assets/docker_build_t2.png" style="width: 60%; height: auto;"/>  

중간에 임시에 생성된 intermediate 컨테이너가 삭제가 되고 빌드 이미지가 만들어진다.  

아래 명령어를 사용하여 생성된 도커 이미지를 확인 할 수 있다.


```bash
docker images
```  

<img src="./assets/docker_images.png" style="width: 60%; height: auto;"/>

로컬에서 생성된 이미지를 공유하기 위해서는 Docker Hub 에 저장을 해야 하고 그 전에  Docker Hub에서 계정을 생성한다. ( private repository 는 2개만 가능 )   

계정 생성 후에 tagging 을 하고 push를 한다.  
tag 명령어 뒤에는 로컬 이미지 이름 , 다음에는 도커허브 이미지 이름을 입력.

```bash
docker tag edu1 (본인 도커 허브 ID)/edu1
docker push (본인 도커 허브 ID)/edu1
```  

<img src="./assets/docker_edu2_push.png" style="width: 60%; height: auto;"/>

Docker Hub에 페이지에서 push된 이미지를 확인한다.

<img src="./assets/dockerhub_edu2_image.png" style="width: 60%; height: auto;"/>

실행 중인 컨테이너를 확인하고 40003  포트를 사용하는 컨테이너를 stop 한다.
```bash
docker ps
docker stop (컨테이너id)
```  

<img src="./assets/docker_ps_stop.png" style="width: 60%; height: auto;"/>

도커 이미지를 실행한다.
- -d : Detached 모드(백그라운드)
- --name : 컨테이너에 이름을 부여한다.
- -p : 포트 ( 외부접속포트 : 컨테이너 포트)
- 맨 마지막에 도커 이미지 이름  

```bash
docker run -d --name my-python -p 40003:5000 (본인 도커 허브 ID)/edu1
```  

docker ps 명령어로 정상적인지 확인한다.

<img src="./assets/docker_run_d.png" style="width: 60%; height: auto;"/>

docker ps 로 아무 것도 없으면 docker ps -a 명령어도 kill 된 컨테이너를 확인하고
docker logs 명령어로 로그를 확인한다.


```bash
docker logs (컨테이너id)
```  
<img src="./assets/docker_logs.png" style="width: 60%; height: auto;">  

에러가 발생한 도커는 같은 이름으로 docker run 명령어를 실행 하면 에러가 발생하기 때문에 아래 명령어를 실행 후에 위의 docker run 명령어를 다시 실행한다.  

```bash
docker rm (컨테이너 이름)
```

docker 컨테이너 안으로 들어가 본다.

```bash
docker exec -it (컨테이너id)   /bin/sh
```  

컨테이너 내부에서 ls 명령어를 쳐보면 도커 빌드시 복사되었던 소스를 확인 할 수 있다.

<img src="./assets/docker_exec.png" style="width: 60%; height: auto;">  

exit 명령어를 사용하여 컨테이너 내부에서 나올수 있다.  

현재 컨테이너의 내용을 도커 이미지로 저장하고 싶을때는 commit 명령어를 사용한다.
- -m : 뒤에 comment를 적어준다
- 컨테이너 이름 : 현재 실행되는 컨테이너 이름 또는 컨테이너 아이디를 적여준다
- 신규 도커 이미지 : 신규로 생성하고 싶은 로컬 도커이미지 이름을 적어준다  

```bash
docker commit -m "new edu1" (컨테이너 이름) (생성하고싶은 이미지 이름):(버전)
```  

<img src="./assets/docker_commit.png" style="width: 60%; height: auto;">  

docker stats 명령어를 사용하면 컨테이너별로 CPU/MEM을 볼 수 있다.  

```bash
root@jakelee:~# docker stats
```  

<img src="./assets/docker_stats.png" style="width: 80%; height: auto;">

<br/>

도커 추가 명령어
- 정지 중인 컨테이너 삭제 : docker container prune
- 이미지 , 정지되어 있는 컨테이너 , 네트웍크 삭제 : docker system prune
- 도커 이미지 삭제 : docker rmi (도커 로컬 이미지 이름)
- 컨테이너 삭제 : docker rm [CONTAINER_ID] 
- 컨테이너 일시정지 :  docker stop [CONTAINER_ID]

<br/>

## GitHub 계정을 생성

<br/>

###  https://github.com/ 접속하고 계정 생성  

<br/>

### 계정 생성 후에 Repository를  생성한다.

우리는 repository 를 fork할 예정임으로 아래 생성 과정은 생략하고  setting 메뉴에서 repository 선택 한 후에 default branch를 main 에서 master 로 변경한다.  

> 아래 내용 스킵

아래와 같이 이름 입력를 하고 README file check 를 한다.  

<img src="./assets/repository_create.png" style="width: 80%; height: auto;"/>

default 브랜치를 main에서 master로 변경한다. ( 맨 하단 setting 클릭하여 설정)

<img src="./assets/default_branch_modify.png" style="width: 60%; height: auto;"/>

<br/>

> 여기서 부터 시작

<br/>

### 교육용 repository인 https://github.com/shclub/edu1 를 fork 하여 본인의 Repository에 복사한다. 

총 4개 화일을 만들고 내용을 복사한다.  

<img src="./assets/shclub_edu_file.png" style="width: 60%; height: auto;"/>

   - 샘플은 pyhon flask 로 구성

<br/><br/>

##  Docker Hub 계정을 생성 

<br/>

### https://hub.docker.com/ 접속하고 계정 생성 (향후 사내에서 개발시는 d-space Nexus 사용)  

<br/>

### Docker 연동 테스트를 한다.

<br/>

기존에 로그인 사람의 계정이 있을수 있기 때문에 로그아웃을 먼저한다.  

```bash
docker logout 
```  

다시 로그인을 하고  본인 id와 비밀번호를 입력한다.  

```bash
docker login 
```  

<br/>

tag를 하여 새로운 이름을 생성하고  docker hub에 push 한다.   


```bash
docker tag hello-world (본인id)/hello-world
docker push (본인id)/hello-world  
```

- 권한 에러 발생시 docker login 한다
```bash
docker login 
```

<img src="./assets/docker_denied.png" style="width: 60%; height: auto;"/>

정상적으로 로그인후 push를 한다.

```bash
docker push (본인id)/hello-world  
```     

<img src="./assets/docker_push.png" style="width: 60%; height: auto;"/>

도커허브 본인 계정에서 도커 이미지 생성 확인  

<img src="./assets/docker_hub_world.png" style="width: 60%; height: auto;"/>


도커 이미지가 Private으로 되어 있으면 Public 으로 변경한다. 
- 개인 계정은 2개의 private 만 가능

setting 으로 이동하여 Make public 클릭후 repository 이름을 입력후 Make Public 클릭  

<img src="./assets/docker_hub_make_public.png" style="width: 60%; height: auto;"/>

<br/><br/>

## Jenkins 설정

<br/>

### 일반 사용자 계정을 생성한다  

웹 브라우저에서 http://211.252.85.148:9000/ 로 이동하여 로그인 한다.
교육 계정은 기 생성 되어 있다. ( edu1 ~ edu35 )  

비밀번호는 계정 이름과 동일.  

<br/>


Manage Jenkins -> Manage Users  로 이동한다. 사용자 생성 버튼 클릭 후 사용자 생성.  
<img src="./assets/jenkins_user.png" style="width: 80%; height: auto;"/>


<br/>

### 계정 별 권한 부여방법 

<br/>

Configure Global Security로 이동  

<br/>

> admin 계정으로 테스트 할 예정 으로  skip  한다.  
    
<img src="./assets/configure_global_security.png" style="width: 80%; height: auto;"/>


생성한 계정을 입력하고 Add 클릭 추가후 권한 설정은 일단 Ovrall 체크 후 저장  
Project-based Matrix Authorization Strategy 체크 후 권한 설정  

<img src="./assets/configure_global_security2.png" style="width: 80%; height: auto;"/>

<br/>

### Github token 생성하기

<br/>

웹브라우저에서 Github 로그인하고 
Jenkins 에서 github repository 인증을 위해 사용할 token 을 생성한다.  

- Settings - Developer settings - Personal access tokens - Generate token  
선택해서 토큰 생성  

Github 사이트의 오른쪽 상단 본인 계정의 Setting으로 이동한다. ( 프로젝트의 setting이 아님 )  

<img src="./assets/github_token_setting1.png" style="width: 60%; height: auto;"/>

<br/>

Expiration 은 No Expiration으로 선택하고 repo, admin:repo_hook , write:packages, delete:packages 만 체크하고  Generation Token 버튼 클릭해서 토큰 생성  
 
<img src="./assets/github_token_setting2.png" style="width: 80%; height: auto;"/>


복사 아이콘을 클릭하여 토큰 값을 복사한다. 
- 다시 페이지에 들어가면 보이지 않아서 복사 후 저장 필요  

<img src="./assets/github_token_setting3.png" style="width: 80%; height: auto;"/>

<br/>

### GitHub Credential을 생성한다.  

<br/>

Jenkins가 GitHub에서 Code를 가져올 수 있도록 Credential을 추가하자

- Manage Jenkins -> Manage Credential -> System 으로 이동한다.

<img src="./assets/jenkins_github_credential1.png" style="width: 80%; height: auto;"/>

Global Credential 클릭  

<img src="./assets/jenkins_github_credential2.png" style="width: 80%; height: auto;"/>

Add Credential를 클릭하면 계정 설정하는 화면이 나온다.  

<img src="./assets/jenkins_github_credential3.png" style="width: 80%; height: auto;"/>

Kind는  Username with password 를 선택해주시면 됩니다.  

> Username 은 본인의 Github ID 를 선택해주시면 됩니다. ( 이메일 아님 )  

ID는 본인이 원하는 식별자를 넣어준다.  

> password는 이전에 발급받은 Github Token 값을 입력한다.  
       
<img src="./assets/jenkins_github_credential4.png" style="width: 80%; height: auto;"/>

<br/>

### Docker Hub Credential을 생성한다.  

<br/>

Jenkins가 Docker Hub에 Image를 push 할 수 있도록 Credential을 추가하자

- Manage Jenkins -> Manage Credential -> System  -> Global Credential 로 이동한다.

<br/>

> 바로 이동 : http://211.252.85.148:9000/credentials/store/system/  

<br/>

Add Credential를 클릭하면 계정 설정하는 화면이 나온다.  

Kind는  Username with password 를 선택해주시면 됩니다.  

Username 은 본인의 Docker Hub ID 를 선택해주시면 됩니다. ( 이메일 아님 )  

ID는 본인이 원하는 식별자를 넣어준다.  

password는 이전에 Docker Hub 본인 계정의 비밀번호 값을 입력한다.  

<img src="./assets/jenkins_dockerhub_credential.png" style="width: 80%; height: auto;"/>

<br/>

GitHub와 Docker Hub Credential 이 생선된 것을 확인한다.  

<img src="./assets/jenkins_github_dockerhub_credential.png" style="width: 60%; height: auto;"/>

<br/>

### CI 파이프 라인을 구성한다.
        
<br/>

메인 화면 좌측 메뉴에서 새로운 Item 선택  

<img src="./assets/pipeline_newitem.png" style="width: 60%; height: auto;"/>

item 이름을 입력하고 Pipeline 을 선택 후에 OK  

<img src="./assets/pipeline_main.png" style="width: 60%; height: auto;"/>

로그 Rotation을 5로 설정한다. 5개의 History 를 저장한다. 

<img src="./assets/pipeline_log_rotation.png" style="width: 80%; height: auto;"/>

Jenkins 홈 폴더로 이동하고 아래 명령어를 실행한다.

- 홈 폴더 확인은  대쉬보드에서 Manage Jenkins -> Configure System으로 이동하여 보면 상단에 표시  

<img src="./assets/jenkins_home_folder.png" style="width: 60%; height: auto;"/>  


```bash
cd /var/lib/jenkins
ls ./jobs/edu1/builds
```    

7개의 History중  3,4,5,6,7  5개만 저장된것을 확인 할 수있다.    

<img src="./assets/jenkins_build_folder.png" style="width: 40%; height: auto;"/>  


대쉬보드에서도 History를 확인 할 수 있다.  

<img src="./assets/jenkins_build_history.png" style="width: 40%; height: auto;"/>  

<br/>

Github Project URL을 설정하고 Git Parameter 를 체크하고 Parameter Type은 교육을 위한 용도 임으로 Branch를 선택한다. ( Tag는 Release를 위한 빌드 방식으로 그 당시 snapshot 이다. )    

Branch의 default는 orgin/master를 설정한다.  

<img src="./assets/pipeline_git.png" style="width: 80%; height: auto;"/>

<br/> 

* Tag 를 사용한 빌드는 맨 아래 부분을 참고한다.

<br/>

Build Triggers - GitHub hook trigger for GITScm polling 선택  
Repository 에 Git Url을 입력한다.  
Credential에 Jenkins에서 생성한 github_ci를 선택하여 추가한다.
 
<img src="./assets/pipeline_git2.png" style="width: 80%; height: auto;"/>


Script Path는 Jenkinsfile 로 설정한다.  
github에 대소문자 구문하여 Jenkinsfile 이 있어야함.  
Save 버튼을 클릭하여 저장한다.  

<img src="./assets/pipeline_scriptpath.png" style="width: 60%; height: auto;"/>

<br/>

### 빌드 실행

<br/>

빌드 하기 전에 Jenkins 화일로 이동하여 docker hub 의 repository와 docker credential은 본인의 계정으로 설정한다.  

대쉬보드에서 Build With Parameter를 선택하고 Branch 선택 후 빌드 한다.  

<img src="./assets/jenkins_first_build.png" style="width: 60%; height: auto;"/>

빌드가 진행 되는 것을 단계별로 확인 할 수 있다.  

<img src="./assets/build_stage2.png" style="width: 60%; height: auto;"/>

에러가 발생하면 해당 단계에서 마우스 오른쪽 버튼을 클릭하여 로그 확인 할수 있다.  
또한 왼쪽 하단의 Build History 에서 해당 빌드 번호를 클릭하여 자세한 에러를 볼수 있다.  

<img src="./assets/build_error.png" style="width: 60%; height: auto;"/>

Console Output을 선택하고 에러를 확인 할 수 있다.  

<img src="./assets/build_console_output.png" style="width: 60%; height: auto;"/>

아래 에러는 docker hub에서 repository가 private 으로 설정이 되어 발생한 에러이고 위에 설명한 대로 public 으로 변경하면 에러가 발생하지 않는다.  

<img src="./assets/build_docker_access_denied.png" style="width: 60%; height: auto;"/>

대쉬보드에서 해당 파이프라인인 edu1을 선택하고 다시 빌드 한다.  

<img src="./assets/build_again.png" style="width: 60%; height: auto;"/>

성공적으로 완료된 화면을 볼 수 있다.   

<img src="./assets/build_finish.png" style="width: 60%; height: auto;"/>

Docker Hub에서 정상적으로 생성된 이미지를 확인 할수 있다.  

<img src="./assets/build_dockerhub_check.png" style="width: 60%; height: auto;"/>

* Docker build에 에러가 발생하는 경우가 있는데 Jenkins plugins가 정상적을
  설치가 안되어 있을수 있다.   
  docker 검색하여 제대로 설치가 되어 있는지 확인 하고 없으면 재 설치 한다.

<br/>

### Docker pull 및 실행 테스트  

<br/>

터미널로 VM 서버에 접속하여 생성된 도커이미지를 다운로드(pull)하고 실행 (run)  

```bash
docker pull shclub/edu1
```  

<img src="./assets/docker_pull_edu1.png" style="width: 60%; height: auto;"/>  

```bash
docker run -p 40003:8080 shclub/edu1
```
Python Flask 가 정상적으로 로드가 된걸 확인 할 수 있다.

<img src="./assets/docker_run_edu1.png" style="width: 60%; height: auto;"/>  

브라우저에서 localhost:40003 호출하여 Hello World 확인
 
<img src="./assets/flask_web_edu1.png" style="width: 60%; height: auto;"/>  

<br/>  

- 과제 : github webhook를 통한 빌드 자동화   ( git push  하면 자동 빌드 )        
    
<br/>

### Jenkinsfile 설명  

<br/>

Jenkins 화일에서 github와 docker credential 은  Jenkins 설정에서 Credential을 생성한
id를 입력하면 된다.   

반드시 본인이 만든 값으로 Jenkins파일의 수정해야 함.  

<img src="./assets/pipeline_credential.png" style="width: 80%; height: auto;"/>  

Jenkins에 설정된 credential  

<img src="./assets/jenkins_github_dockerhub_credential.png" style="width: 80%; height: auto;"/>  


Jenkins Stage View 를 통해 Step별 진행 사항을 볼수 있다.  


<img src="./assets/jenkins_stage_view.png" style="width: 60%; height: auto;"/>  

<br/>

### Jenkins 환경변수

<br/>

env 환경변수는 다음과 같은 형식 env.VARNAME으로 참조될 수 있다. 대표적인 env의 property는 아래와 같다. 

<img src="./assets/jenkins_env_variable.png" style="width: 80%; height: auto;"/>  

<br/>

currentBuild 환경변수는 현재 빌드되고 있는 정보를 담고있다. 보통 readonly 옵션인데 일부 writable한 옵션이 존재한다. 대표적인 currentBuild의 property는 아래와 같다.

<img src="./assets/jenkins_current_variable.png" style="width: 80%; height: auto;"/>    


환경 변수 사용 예제.

```bash
pipeline {
    agent any
    stages {
        stage('Example') {
            steps {
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
            }
        }
    }
}
```



<br/>

### Tag를 사용한 Jenkins 빌드

<br/>

Tag를 사용한 빌드는 운영(Release)를 위해 주로 사용하며 Tagging하는 순간의 snapshot 이다.  

Git Parameter에서 Tag를 선택하고의 default는 RB.0.1 을 임으로 설정한다.  

<img src="./assets/jenkins_tag.png" style="width: 60%; height: auto;"/>  

<br/>

GitHub 로 이동 한후 Repository를 선택 한 후 code Tab 으로 들어간다.  
Tags 아이콘을 클릭한다.

<img src="./assets/github_tag1.png" style="width: 60%; height: auto;"/>  

Tags를 선택하고 Create new release 버튼을 클릭한다.  

<img src="./assets/github_tag2.png" style="width: 60%; height: auto;"/>  

Choose a tag를 클릭하면 tag이름을 입력하는 text 박스가 나온다.

<img src="./assets/github_tag4.png" style="width: 60%; height: auto;"/>  

생성하고 싶은 Tag 이름을 입력한다.  
jenkins pipeline에서 RB.0.1을 기본값으로 설정을 해서 같은 이름으로 입력한다.  
입력창 아래 생성된 + Create new tag : RB.0.1 클릭

<img src="./assets/github_tag5.png" style="width: 60%; height: auto;"/>  

Title 값을 입력 ( 원하는 이름 아무거나 입력 ) 하고 Publish release 버튼을 클릭한다

<img src="./assets/github_tag6.png" style="width: 60%; height: auto;"/>

<br/>

Jenkins 대쉬보드에서 Build with Parameters 선택하면 Tag이름이 RB.0.1로 기본값이 설정된다.  

Tag를 선택하고 Build 버튼을 클릭하면 해당 Tag의 소스로 빌드가 된다.

<img src="./assets/jenkins_tag_build.png" style="width: 60%; height: auto;"/>  

<br/>