
# Chapter 4 
   

ArgoCD는 GitOps를 구현하기 위한 컨테이너에 최적화된 CD 툴

1. GitOps 란 : https://coffeewhale.com/kubernetes/gitops/argocd/2020/02/10/gitops-argocd/

2. ArgoCD 설치 및 기능 설명

3. Github에 배포 설정

4. 배포 실습 ( Blue/Green , Canary , Rollback )

 
<br/>

##  GitOps

### GitOps 개요 

개발자와 운영자의 소통, 협업, 통합을 강조하는 DevOps는 많이들 들어보셨을 겁니다.  

GitOps는 DevOps의 실천 방법 중 하나로 애플리케이션의 배포와 운영에 관련된 모든 요소들을 Git에서 관리(Operation) 한다는 뜻입니다.  

<img src="./assets/gitops_overview.png" style="width: 80%; height: auto;"/>     

아주 간단하게 말해서 GitOps는 Kubernetes Manifest파일을 Git에서 관리하고, 배포할때에도 Git에 저장된 Manifest로 클러스터에 배포하는 일련의 과정들을 의미합니다.   

<br/>

GitOps 원칙  

- 모든 시스템은 선언적으로 선언되어야 함  
“선언적(declarative)”이라 함은 명령들의 집합이 아니라 사실(fact)들의 집합으로 구성이 되었음을 보장한다는 의미입니다.
쿠버네티스의 manifest들은 모두 선언적으로 작성되었고 이를 Git으로 관리한다면 versioning과 같은 Git의 장점과 더불어, SSOT(single source of truth)를 소유하게 됩니다.  
- 시스템의 상태는 Git의 버전을 따라감  
Git에 저장된 쿠버네티스 manifest를 기준으로 시스템에 배포되기 때문에 이전 버전의 시스템을 배포하고싶으면 git revert와 같은 명령어를 사용하면 됩니다.
- 승인된 변화는 자동으로 시스템에 적용됨  
한 번 선언된 manifest가 Git에 등록되고 관리되기 시작하면 변화(코드수정 등)가 발생할때마다 자동으로 시스템에 적용되어야 하며, 클러스터에 배포할때마다 자격증명은 필요하지 않아야 합니다.  
- 배포에 실패하면 이를 사용자에게 경고해야 함  
시스템 상태가 선언되고 버전 제어 하에 유지되었을 때 배포가 실패하게되면 사용자에게 경고할 수 있는 시스템을 마련해야합니다.

<br/>

GitOps Repository  

GitOps Pipeline을 설계할때에는 Git Repository를 최소 두개이상 사용하는 것을 권장합니다.  


<img src="./assets/gitops_pipeline.png" style="width: 80%; height: auto;"/>  

- App Repo : App 소스코드와, 배포를 위한 Manifest 파일  
- 배포 환경 구성용 Repo : 배포 환경에 대한 모든 Manifest (모니터링, 서비스, MQ 등)들이 어떤 버전으로 어떻게 구성되어있는지 포함  


<br/>

GitOps 배포 전략  

두가지 방법이 있습니다.

- Push Type  
Git Repo가 변경되었을 때 파이프라인을 실행시키는 구조입니다.

	<img src="./assets/gitops_push.png" style="width: 80%; height: auto;"/>

	배포 환경의 개수에 영향을 받지 않으며 접속 정보를 추가하거나 수정하는 것만으로도 간단하게 배포 환경을 추가하거나 변경할 수 있습니다.  
	아키텍처가 쉬워 많은 곳에서 사용하고 있으나, 보안정보가 외부로 노출될 수 있다는 단점이 있습니다.

- Pull Type  
배포하려는 클러스터에 위치한 별도의 오퍼레이터가 배포역할을 대신합니다.  

	<img src="./assets/gitops_pull.png" style="width: 80%; height: auto;"/>

	해당 오퍼레이터는 Git Repo의 Manifest와 배포환경을 지속적으로 비교하다가 차이가 발생할 경우, Git Repo의 Manifest를 기준으로 클러스터를 유지시켜 줍니다.  
	
	또한 Push Type과 달리 오퍼레이터가 App과 동일한 환경에서 동작중이므로 보안 정보가 외부에 노출되지 않고 실행할 수 있습니다.

<br/>


### ArgoCD 둘러보기

<br/>

ArgoCD를 설치하여 로그인하면 가장 먼저 볼 수 있는 화면은 아래와 같습니다.  

지금까지 생성한 배포 App의 리스트를 보여주는 화면입니다.   새로운 배포를 관장하는 App을 생성해 보기 위해 New App 버튼을 눌러보겠습니다.  

<img src="./assets/argocd_newapp1.png" style="width: 80%; height: auto;"/>  

새로운 배포을 책임지는 App을 생성하는 화면입니다.    

<img src="./assets/argocd_newapp2.png" style="width: 80%; height: auto;"/>  

- Application Name: App의 이름을 적습니다.  
- Project: 프로젝트를 선택하는 필드입니다. 쿠버네티스의 namespace와 비슷한 개념으로 여러 App을 논리적인 project로 구분하여 관리할 수 있습니다.  
- Sync Policy: Git 저장소의 변경 사항을 어떻게 sync할지 결정합니다. Auto는 자동으로 Git 저장소의 변경사항을 운영에 반영하고 Manual은 사용자가 버튼 클릭을 통해 직접 운영 반영을 해줘야 합니다.
- Repository URL: ArgoCD가 바라볼 Git 저장소를 의미합니다.  
- Revision: Git의 어떤 revision (HEAD, master branch 등)을 바라 볼지 결정합니다.
- Path: Git 저장소에서 어떤 디렉토리를 바라 볼지 결정합니다. (dot(.)인 경우 root path를, 디렉토리 이름을 적으면 해당 디렉토리의 배포 정의서만 tracking 합니다.)
- Cluster: 쿠버네티스의 어느 클러스터에 배포할지를 결정합니다.
- Namespace: 쿠버네티스 클러스터의 어느 네임스페이스에 배포할지를 결정합니다.
- Directory Recurse: path아래의 디렉토리를 재귀적으로 모니터링하여 변경 사항을 반영합니다.  

<br/>

아래의 깃헙 레포지토리를 예시로 배포해 보겠습니다. 간단하게 nginx 컨테이너를 생성하고 서비스를 붙여주는 앱입니다.  

GitOps repository 예시: https://github.com/shclub/edu5.git  

본인의 github에서 위 repository를 fork 합니다. 

아래 처럼 deployment.yaml , service.yaml , ingress.yaml이 생성된걸 확인 할 수 있습니다.  

ingress.yaml 화일의 host 정보는 본인 VM Public IP로 변경 합니다.  

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mynginx
spec:
  replicas: 1
  selector:
    matchLabels:
      run: mynginx
  template:
    metadata:
      labels:
        run: mynginx
    spec:
      containers:
      - image: nginx
        name: mynginx
        ports:
        - containerPort: 80
```  
  

```bash
apiVersion: v1
kind: Service
metadata:
  name: mynginx
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: mynginx
```   

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mynginx-traefik-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: "/"
    kubernetes.io/ingress.class: traefik
spec:
  rules:
  - host: mynginx.210.106.105.165.sslip.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: mynginx
            port:
              number: 80
```  

url은 본인의 url로 설정한다.  

- Application Name: caravan
- Project: default
- Sync Policy: manual
- Repository URL: https://github.com/shclub/edu5.git
- Revision: HEAD
- Path: .
- Cluster: in-cluster
- Namespace: default
- Directory Recurse: Unchecked

<br/>

우리는 아래와 같이 설정을 한다.  


<img src="./assets/argocd_practice1.png" style="width: 80%; height: auto;"/>  
<img src="./assets/argocd_practice2.png" style="width: 80%; height: auto;"/>   


위와 같이 값을 설정해주고 `Create` 버튼을 클릭합니다.  

`SYNC` 버튼을 눌러 ArgoCD가 변경 사항을 확인하여 단일원천의 진실에 따라 운영 환경을 그에 맞게 변경하도록 하겠습니다.    

<img src="./assets/argocd_practice3.png" style="width: 80%; height: auto;"/>   

App을 클릭하면 아래와 같이 Ingress, Service 리소스와 nginx pod가 생성된 것을 UI로 확인하실 수 있습니다.  

<img src="./assets/argocd_practice4.png" style="width: 80%; height: auto;"/>   

파란색 아이콘이 보이면 정상적으로 수행이 된것 있고 mynginx pod 가 정상적으로 생성 되었는지 확인합니다.    

```bash
root@jakelee:~# kubectl get po
NAME                                  READY   STATUS    RESTARTS   AGE
flask-edu4-app-74788b6479-qgs2j       1/1     Running   0          6d21h
flask-edu4-app-74788b6479-l7gkx       1/1     Running   0          6d21h
flask-edu4-app-74788b6479-nmcvv       1/1     Running   0          6d21h
flask-edu4-app-74788b6479-f2kcp       1/1     Running   0          6d21h
flask-edu4-app-74788b6479-rlght       1/1     Running   0          6d21h
hpa-example-deploy-59bf97fcc6-6nxjs   1/1     Running   0          6d1h
inspekt-deployment-c8d9f5dcf-2slpx    1/1     Running   0          23h
mynginx-69d586ff67-bmh6m              1/1     Running   0          6m23s
```  

Ingress를 설정 했기 때문에 web browser 에서 도메인으로 접속해봅니다.      
정상이면 아래와 같이 nginx welcome 화면이 보입니다.  

<img src="./assets/argocd_practice5.png" style="width: 80%; height: auto;"/>   

<br/>

앞에 App을 설정할때 sync-policy를 manual 설정하였습니다. 

아래에 Auto-Sync 버튼을 활성화하게 되면 Automatic이 되어 매번 사람이 직접 변경사항을 ArgoCD에게 알릴 필요 없이 ArgoCD가 주기적으로 Git 레포지터리의 변경사항을 확인하여 변경된 부분을 적용하게 됩니다.   

이때 두가지 옵션을 추가적으로 줄 수 있습니다.

- Prune Resources: 변경 사항에 따라 리소스를 업데이터할 때, 기존의 리소스를 삭제하고 새로운 리소스를 생성합니다. Job 리소스처럼 매번 새로운 작업을 실행해야 하는 경우 이 옵션을 사용합니다.  

- Self Heal: 해당 옵션을 활성화 시키면 ArgoCD가 지속적으로 git repository의 설정값과 운영 환경의 값의 싱크를 맞출려고 합니다. 기본적으로 5초마다 계속해서 sync를 시도하게 됩니다. (default timeout)  

해당 예시에서는 Auto-sync만 활성화 시켜보겠습니다.   그런 다음, 이제 git repository의 deployment replica 값을 2로 고쳐서 ArgoCD가 자동으로 변경한 값을 운영 환경에 반영하는지 확인해 보겠습니다.  

<img src="./assets/argocd_practice6.png" style="width: 80%; height: auto;"/> 

AppDetail을 클릭하면 하단에 enable auto sync 버튼을 클릭합니다.  

<img src="./assets/argocd_practice7.png" style="width: 80%; height: auto;"/>   

신규로 pod가 하나 생성되어 2개의 pod가 존재하는 것을 확인 할수 있습니다.  

<img src="./assets/argocd_practice8.png" style="width: 80%; height: auto;"/>   

traffic 흐름을 볼수 도 있고  

<img src="./assets/argocd_practice9.png" style="width: 80%; height: auto;"/>   

각 node의 resource  들을 볼 수 있고 pod의 리소스도 확인 가능 하다.    

<img src="./assets/argocd_practice10.png" style="width: 80%; height: auto;"/>   


기본적으로 swagger를 제공하고 있어 ArgoCD 의 API를 사용 할 수 있다.    
- 사용법 : http:// < 본인의 argocd URL > / swagger-ui    

<img src="./assets/argocd_swagger.png" style="width: 100%; height: auto;"/>   


<br/>

기본 사용법   
 - https://argo-cd.readthedocs.io/en/stable/getting_started/#creating-apps-via-ui

<br/>

추가 배포 관련  Hands-on은 아래 문서를 참고 한다.

- 문서를 참고한다. [ArgoCD Hands-On ](./argocd_hands_on.md)  

<br/>






