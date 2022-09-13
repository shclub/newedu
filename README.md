# 본 과정에 대해  ( Cloud Native 입문 과정 )
 
본 교육 과정은 Cloud Native 입문 과정으로 이론 및 실습을 수행한다. ( 2일 오프라인 교육 )
 

문의 :  이석환 ( seokhwan.lee@kt.com / shclub@gmail.com )

<br/>

1. Chapter 1 : 2시간  ( [가이드 문서보기](./chapter1.md) )  

     - Docker 이해 및 활용 
     - GitHub , Docker 계정 생성 , Jenkins Pipeline 생성하여 CI 실습 

     <br/>

2. Chapter 2 : 2시간  ( [가이드 문서보기](./chapter2.md) )  

     - Github Action , workflow 사용 ( GoodBye Jenkins ) 
     - Docker Compose 설치 및 활용 ( DB 연동 )  

     <br/>

3. Chapter 3 : 2시간   ( [가이드 문서보기](./chapter3.md) )  

     - k8s 이해 및 활용
     - k8s hands-on Basic [ Hands-On 문서보기 ](./k8s_basic_hands_on.md)  

          - 실습 전체 개요
          - kubeconfig 설정 :  oc / kubectl 설치
          - kubectl 활용
          - kubernetes 리소스 ( Pod , Service , Deployment 생성 및 삭제)
          - 배포 ( Rolling Update / Rollback )
          - Serivce Expose ( Ingress / Route )  

     <br/>


4. Chapter 4 : 10분   ( [가이드 문서보기]( https://github.com/shclub/edu/blob/master/chapter9.md ) ) 

     - OKD 4.7 설치 ( kt cloud FlyingCube 2.0 )  
          - NFS 설정 및 접속  
     - ArgoCD 설치 및 설정  ( OKD )  
     - elastic APM 설치 및 설정  ( OKD )  

    <br/>

5. Chapter 5 : 2시간   ( [가이드 문서보기](./chapter4.md) ) 

     - GitOps 설명 
     - kustomize 설명 및 실습
     - k8s에 배포 실습 ( Blue/Green , Canary )  
     - ArgoCD Hands-on [ Hands-On 문서보기 ](./argocd_hands_on.md) 

          - kubectl plugin 설치
          - Blue/Green 배포
          - Canary 배포
          - ArgoCD 계정 추가 및 권한 할당
          - kustomize 사용법
          - ArgoCD remote Cluster 에서 배포 하기 

     <br/>

6. Chapter 5 : 1시간   ( [가이드 문서보기](https://github.com/shclub/edu/blob/master/k8s_middle_hands_on.md) ) 

     - Storage Volume  ( PV/PVC , DB 설치 + NFS )
          - MariaDB NFS 에 설치 ( /w Helm Chart ) 
     - NFS 라이브러리 설치 ( Native Kubernetes )
     - Service - Headless, Endpoint, ExternalName
     - EFK APM Agent 설정  ( React / SpringBoot )
     - Helm Chart 개념 및 설명
     - React/SpringBoot/MariaDB 3-tier 구조 한번에 배포 하기
          - Helm Umbrella 패턴 ( /w SubChart )
          - ArgoCD Apps-of-Apps 패턴 
     - Elastic APM 활용한 로그 확인
     
     <br/>

<br/>

## Jenkins 접속 정보
 
<br/>

웹브라우저에서 접속 가능.    

http://211.252.85.148:9000/   


- 계정 : <edu + 순번>  ( 예: edu1 )
- 비밀번호 : 사전 공지  


<br/>

---

## OKD 접속 정보
 
<br/>

```bash
oc login https://api.211-34-231-81.nip.io:6443 -u shclub-admin -p <비밀번호> --insecure-skip-tls-verify
```  

- 계정 : <edu + 순번 + `-admin`>  ( 예: edu1-admin )
- 비밀번호 : 사전 공지  


<br/>


---
## ArgoCD 접속 정보
 
<br/>


OKD : https://argocd-argocd.apps.211-34-231-82.nip.io/applications    


K3S : https://211.252.87.34:30000/  ( 임시 )

- 계정 : edu + 순번  ( 예: edu1 )
- 비밀번호 : 사전 공지  

<br/>

---

## KT Cloud KTP 상품
 
<br/>

```bash
https://cloud.kt.com/portal/user-guide/Container-container-guide
```  

<br/>

---

## NGINX YAML Sample
 
<br/>

nginx.yaml
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```


---

## 조별 과제 VM 서버

rookie1 : 211.253.37.96  

rookie2 : 211.253.37.107  

rookie3 : 211.43.14.85  

rookie4 : 211.253.37.149  

rookie5 : 211.253.37.151  
