
# Spring boot + AWS 

````
Spring Boot 프로젝트를 Amazon Cloud에 배포 후 Route53에서 생성한 도메인과 연결
````

## 아키텍처

![](https://i.imgur.com/PndqxUX.png)
## 시나리오

````
📃

1. VPC를 생성하여 가상 네트워크를 만든다.  
   
2. 2개의 가용영역을 만들고 각각 Public Subnet과 Private Subnet을 생성한다.

3. Public/Private 라우팅 테이블을 만들어 Subnet과 연결한다. 
   
4. 인터넷 통신을 위한 인터넷 게이트웨이를 Public 라우팅 테이블과 연결한다. 
   
5. Private 서브넷이 아웃바운드를 할 수 있도록 NAT Gateway를 생성 후 Public 서브넷에 추가한다. 그리고 Private 라우팅 테이블에 NAT Gateway를 연결한다. 
   
6. Spring Boot 프로젝트 생성 후 이미지를 위한 도커파일을 생성한다. 
   
7. Amazon에서 이미지를 담을 ECR을 생성 후 Docker 명령어를 통해 빌드 후 이미지를 푸쉬한다. 
   
8. EKS 를 생성하여 kubectl을 통해 ALB를 연결한다. 
   
9. Route53에 도메인 생성 시 ALB의 DNS를 입력 후 연결한다.
````

## VPC

![](https://i.imgur.com/Y5bWfxH.png)

````
📎

이름 태그 (jachu-vpc) / IPv4 CIDR (10.0.0.0/16) 입력 후 생성
````

## 서브넷

![](https://i.imgur.com/NtCtqOR.png)

````
📎 

각각의 가용영역( ap-northeast-2a / ap-northeast-2c)에 Public / Private 서브넷 생성 (가용영역 2개일 시)

네트워크 더 작은 네트워크 단위로 분할하기 위해 10.0.0.0/16 네트워크를 여러개의  10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24, 10.0.4.0/24 의 서브넷으로 나눔

  
퍼블릭 서브넷일 경우엔 IPv4 주소 자동 할당 활성화 필수
````

## 라우팅 테이블

![](https://i.imgur.com/2u9Gp95.png)

````
📎 

생성한 VPC를 선택하여 public/private 라우팅 테이블 생성 (ex. jachu-public-rtb, jachu-private-rtb)
````

![](https://i.imgur.com/NPfH6YI.png)

````
📎 

생성된 라우팅 테이블을 클릭하여 각각 public / private에 맞게 서브넷 연결

(public routing table => public 서브넷)
(private routing table => private 서브넷)
````

## 인터넷 게이트웨이

![](https://i.imgur.com/ynKkeoI.png)

![](https://i.imgur.com/acbp02h.png)

````
📎 

인터넷 게이트웨이를 생성하여 public routing table에 라우팅 추가
````

## NAT  게이트웨이

![](https://i.imgur.com/nudhD2v.png)


````
📎 

NAT 게이트웨이를 퍼블릭 서브넷에 위치하도록 지정해주고 생성 후 private routing table에 라우팅 추가
````


## 탄력적 IP

![](https://i.imgur.com/0mr77Wb.png)


````
📎 

탄력적 IP 생성 후 NAT 게이트웨이에 할당하여 공인 IP를 가지도록 설정
````
## ECR

![](https://i.imgur.com/Ua0uXhJ.png)

````
📎 

Repository 이름 입력 후 생성
````

## Docker 이미지 생성 및 푸시

```
## 1. Registry 인증
aws ecr get-login-password --region [Region] | docker login --username AWS --password-stdin [인증 계정]

## 2. 프로젝트 빌드

## 3. jar 파일이 있는 위치로 이동
cd build/libs

## 4. 도커에 빌드
docker build -t jachu

## 5. 이미지 태그 설정
docker tag jachu:latest [인증 계정]/jachu:latest 

## 6. 해당 Repository에 Push
docker push [인증 계정]/jachu:latest
```

## EKS

![](https://i.imgur.com/eXmIApw.png)

````
📎 

클러스터 이름 입력 후 생성
````

![](https://i.imgur.com/jmucIBo.png)

````
📎 

VPC 선택후 ENI를 배치할 서브넷을 선택
private 서브넷에 인스턴스를 만들거기 때문에 각 가용영역의 프라이빗 서브넷으로 선택
````

## ALB

![](https://i.imgur.com/y0zamrX.png)

````
📎 

프로젝트 위치에 세가지 yaml 파일 생성 (ex. 프로젝트 경로/eks/)
````

### ingress.yaml

![](https://i.imgur.com/084IJQi.png)


### deployment.yaml

![](https://i.imgur.com/tCHihl9.png)

### service.yaml

![](https://i.imgur.com/tb4QXj3.png)

### AWS Load Balancer Controller 설치

1. IAM 정책 생성

````
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json 

aws iam create-policy 
--policy-name AWSLoadBalancerControllerIAMPolicy 
--policy-document file://iam_policy.json
````

2.  IAM 역할 생성 및 정책 연결

````
eksctl create iamserviceaccount 
--cluster=jachu-cluster 
--namespace=kube-system 
--name=aws-load-balancer-controller 
--role-name AmazonEKSLoadBalancerControllerRole 
--attach-policy-arn=arn:aws:iam::[rootId]:policy/AWSLoadBalancerControllerIAMPolicy 
--approve
````

3.  AWS LoadBalancer Controller 설치

````
helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system 
--set clusterName=jachu-cluster 
--set serviceAccount.create=false 
--set serviceAccount.name=aws-load-balancer-controller 
````


### Yaml 파일 적용

````
kubectl apply -f ingress.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
````

### 적용 확인

````
kubectl get svc
kubectl get pod
````

### 만약 오류가 있을 시

````
// AWS Load Balancer Controller pod 로그
kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-load-balancer-controller
kubectl logs -n kube-system <aws-load-balancer-controller-pod-name>

// AWS Load Balancer Controller 로그
kubectl logs -n kube-system deployment/aws-load-balancer-controller 

// ingress 확인
kubectl describe ingress jachu-ingress

// 서비스 pod 로그 확인
kubectl -n default logs <pod-name>
kubectl describe pod <pod명>
````

![](https://i.imgur.com/Gy17GmT.png)

````
📎 

적용이 잘 되었다면 AWS 로드밸런서에 자동으로 생성된 것을 확인 가능
연결된 대상그룹도 Healthy 상태인지 확인!
````

## Route53

![](https://i.imgur.com/pxOXmSs.png)

````
📎 

호스트를 생성 (레코드유형은 CNAME)
````

![](https://i.imgur.com/ngD4qOw.png)

````
📎 

생성된 레코드를 편집하여 값 부분에 만들어진 alb DNS 값을 넣어준다.
````

## 테스트

![](https://i.imgur.com/RH1YEmL.png)

````
📎 

지정한 호스트로 접속해보면 프로젝트에서 설정한 페이지가 보여짐
````
