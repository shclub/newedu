# 본 과정에 대해  ( Cloud Native 입문 과정 )
 
본 교육 과정은 Cloud Native 입문 과정으로 이론 및 실습을 수행한다.
 

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
          - kubeconfig 설정 : kubectl 설치
          - kubectl 활용
          - kubernetes 리소스 ( Pod , Service , Deployment 생성 및 삭제)
          - 배포 ( Rolling Update / Rollback )
          - Serivce Expose ( Ingress / Route )  

     <br/>

4. Chapter 4 : 2시간   ( [가이드 문서보기](./chapter4.md) ) 

     - GitOps 설명 
     - ArgoCD 설치 및 설정 
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

<br/>

## Jenkins 접속 정보
 
<br/>

```bash
http://211.252.85.148:9000/
```  

<br/>

## OKD 접속 정보
 
<br/>

```bash
oc login https://api.211-34-231-81.nip.io:6443 -u shclub-admin -p New1234! --insecure-skip-tls-verify
```  

<br/>

## Node Port용  접속 정보
 
<br/>

```bash
211.34.231.84
```  

<br/>

## ArgoCD 접속 정보
 
<br/>

```bash
https://211.252.87.34:30000/
```  

<br/>

## KT Cloud KTP 상품
 
<br/>

```bash
https://cloud.kt.com/portal/user-guide/Container-container-guide
```  


