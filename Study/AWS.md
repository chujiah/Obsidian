# AWS

![](https://i.imgur.com/CwAMqZh.png)

# **계정 생성**

🙋‍♀️ Root 계정
- AWS 계정 내 모든 AWS 서비스 및 리소스에 접근 가능

👭 IAM 계정 
- (AWS Identity and Access Management), Root 계정의 보안을 위해 IAM을 통해 인증 및 권한을 부여하여 사용

**IAM Identity Center 사용자**
  - IAM Identity Center를 통해 생성되고 관리되는 사용자
  - 중앙 집중식 인증 및 권한 부여를 제공하며, 여러 AWS 계정 및 애플리케이션에 걸쳐 SSO 기능 제공

**IAM 기본 사용자** 
   - AWS 계정 내의 직접 생성되고 관리되는 사용자
   - AWS 리소스에 대한 액세스 관리
<br>
**SSO(Single Sign-On)** 은 사용자가 한번의 로그인으로 여러 시스템이나 애플리케이션에 접근할 수 있게 해주는 인증 방법

# **VPC (Virtual Private Cloud)**

클라우드 환경 AWS 계정 사용자 전용의 가상 네트워크,
서브넷,라우팅 테이블 등 네트워킹 환경을 사용자가 구성 및 제어 가능


![](https://i.imgur.com/Y5bWfxH.png)


````
📎

이름 태그 (jachu-vpc) / IPv4 CIDR (10.0.0.0/16) 입력 후 생성
````

# 서브넷


큰 네트워크를 더 작은 네트워크 단위로 분할 하는 것

- **퍼블릭 서브넷** : 외부 인터넷과 <mark>통신이 가능</mark>한 서브넷 (ex. 웹서버) [아웃바운드,인바운드]

- **프라이빗 서브넷** : 외부 인터넷과 <mark>통신이 불가능</mark>한 서브넷 (ex. 데이터베이스 서버) [아웃바운드]



![](https://i.imgur.com/NtCtqOR.png)


![](https://i.imgur.com/1Encb0k.png)

  
````

📎 

각각의 가용영역( ap-northeast-2a / ap-northeast-2c)에 Public / Private 서브넷 생성 (가용영역 2개일 시)

네트워크 더 작은 네트워크 단위로 분할하기 위해 10.0.0.0/16 네트워크를 여러개의  10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24, 10.0.4.0/24 의 서브넷으로 나눔

  
퍼블릭 서브넷일 경우엔 IPv4 주소 자동 할당 활성화 필수
````

⭐ **가용 영역 (AZ, Availablity Zone)**

이중화와 비슷한 개념으로 AWS 리전 내에서 물리적으로 분리하여 장애 복구 및 지속적인 가용성을 보장

| AZ              | 서브넷                      | IPv4 CIDR                                     |
| --------------- | ------------------------ | --------------------------------------------- |
| ap-northeast-2a | jachu-subnet-public-az1  | 10.0.1.0/24                                   |
| ap-northeast-2a | jachu-subnet-private-az1 | 10.0.3.0/24                                   |
| ap-northeast-2c | jachu-subnet-public-az2  | 10.0.2.0/24                                   |
| ap-northeast-2c | jachu-subnet-private-az2 | 10.0.4.0/24                                   |

# 라우팅 테이블

 VPC 내의 네트워크 트래픽의 경로를 정의

![](https://i.imgur.com/2u9Gp95.png)

````
📎 

생성한 VPC를 선택하여 public 라우팅 테이블 생성 (ex. jachu-public-rtb)
````


![](https://i.imgur.com/ZfxEnvj.png)

![](https://i.imgur.com/9Pa5VFa.png)

````
📎 

라우팅 편집을 클릭하여 추가

0.0.0.0/0 은 10.0.0.0/16 대역을 제외한 모든 대역은 인터넷게이트웨이를 통해 바깥으로 내보내줌

반대로 10.0.0.0/16 대역은 로컬에서 찾음
````

⭐ **인터넷게이트웨이**

VPC를 인터넷에 연결하는 가상 라우터, <br>
퍼블릭 서브넷에 있는 인스턴스가 인터넷으로 나가거나 들어오는 트래픽 처리

![](https://i.imgur.com/yDhEEY4.png)


````
📎 

퍼블릭 라우팅 테이블을 클릭 하여 서브넷 연결 편집을 눌러 퍼블릭 서브넷을 연결

퍼블릭 서브넷은 위에 쓴 것과 같이 외부 인터넷과 통신이 가능한 서브넷이기 때문에 인터넷게이트웨이가 추가된 라우팅 테이블에 연결해야 함
````

# EC2

Amazon Elastic Cloud , AWS가 제공하는 클라우드 컴퓨팅
### 🔍 1. AMI

Amazon Machine Image, EC2 인스턴트를 실행하기 위해 OS 및 소프트웨어 등 세팅 정보를 저장한 단위

![](https://i.imgur.com/GgPqFRC.png)

💡 ***스냅샷과 AMI의 차이는?***

````
AMI의 경우는 Root Volume을 포함하여 EC2에 연결되어 있는 모든 EBS Volume을 백업하는 것

스냅샷의 경우는 각각의 EBS Volume을 백업하는 것

	  AMI : 인스턴스를 백업
	  스냅샷 : 데이터만 백업
````

### 🔍 2. Instance Types

![](https://i.imgur.com/PbBl4bd.png)

![](https://i.imgur.com/a35iC5u.png)

출처: [https://jaychapel.medium.com/ec2-instance-types-comparison-and-how-to-remember-them-bbb96b578aea](https://jaychapel.medium.com/ec2-instance-types-comparison-and-how-to-remember-them-bbb96b578aea)

**EC2 인스턴스**

<mark>EC2에서 제공하는 가상 서버</mark>로 필요에 맞게 인스턴스 유형 및 운영체제를 선택하여 구성 가능

⭐ **다양한 인스턴스 유형**

````
- 범용 인스턴스 : M,T

- 컴퓨팅 최적화 인스턴스 : C

- 메모리 최적화 인스턴스 : R,X

- 스토리지 최적화 인스턴스 : I,D,H

- 가속화된 컴퓨팅 인스턴스 : P,G,D,F,V
````


⭐ **인스턴스 사이즈**

````
[nano < micro < small < medium < large < xlarge < 2xlarge]

→  CPU와 메모리 등 사이즈가 클 수록 늘어나고 성능이 빨라진다.
````


⭐ **인스턴스 타입**

````
Ex) m5a.xlarge

m: 인스턴스 유형
5: 세대
a: 인스턴스 속성
xlarge: 인스턴스 사이즈
````

⭐ **인스턴스 속성**

````
a: AMD 프로세서
g: AWS Gravition 프로세서 (AWS CPU)
i: 인텔 프로세서
d: 인스턴스 스토어 볼륨
n: 네트워크 최적화
b: 블록 스토리지 최적화
e: 추가 스토리지 또는 메모리
z: 고주파
````

### 🔍 3. Key pair

인스턴스 접속을 위한 물리키로 외부에서 EC2를 접속할 때 필요한 키페어 <br>
재발급이 불가하기 때문에 잘 저장해야 함!

![](https://i.imgur.com/mkpULvS.png)

### 🔍 4. 보안 그룹

네트워크를 통해 인스턴스의 접속 허용 범위를 설정

![](https://i.imgur.com/e4VGioq.png)
![](https://i.imgur.com/Ua0ptFt.png)

````
📎

SSH 접속 22번 포트 사용 0.0.0.0/0 (모든 ip) 접속 가능
HTTP 접속 80 포트 사용 0.0.0.0/0 (모든 ip) 접속 가능
````
### 🔍 5. EBS

Elastic Block Storage, 클라우드 가상 하드디스크

![](https://i.imgur.com/qBFCJnu.png)

````
📎

EC2가 종료되어도 별개로 작동하여 유지 가능!
하나의 EBS에 여러 EC2, 하나의 EC2에 여러 EBS 가능!
````

⬛ ***EBS 볼륨

EBS로 생성한 디스크 저장 단위
(Ex. C드라이브, D드라이브)


⬛ ***EBS 볼륨 유형

![](https://i.imgur.com/6bWKPZ4.png)

	[SSD 기반 볼륨]
	 - 범용 SSD 볼륨 
		 테스트 및 개발환경을 비롯한 트랜잭션 애플리케이션에 대한 요금과 성능의 균형을 맞춤 (gp3, gp2)
	 - 프로비저닝된 IOPS SSD 블록 익스프레스 볼륨
		 고성능, 고가용성 및 높은 내구성을 요구하는 애플리케이션에 적합한 EBS 최상위 성능 옵션 (io1, io2)
	
	 [HDD 기반 볼륨]
	  - 쓰루풋 최적화 HDD
		  대용량 순차적 읽기 및 쓰기 작업에 최적화 (st1) [ex. 빅데이터]
	  - 콜드 HDD
		  저비용으로 대용량 데이터를 저장하는데 최적화 (sc1) [ex. 로그 파일]

````
📎

IOPS 수치가 높을수록 데이터 통신이 빠름
````

# 탄력적 IP

Elastic IP, 인터넷을 통해 접속이 가능한 공인 IP 주소 할당

![](https://i.imgur.com/0mr77Wb.png)

````
📎

인스턴스의 public IP가 고정된 IP 주소가 아니기 때문에 인스턴스를 중지하고 실행할 시 IP주소가 변경되어 버림! 그래서 고정적인 IP 주소를 위해 탄력적 IP 할당
````
# NAT Gateway

Network Address Translation (네트워크 주소 변환), <br>
사설 IP를 하나의 공인 IP로 변경하여 외부에 인터넷 통신이 가능하도록 만드는 것

![](https://i.imgur.com/nudhD2v.png)

````
📎 

private 서브넷이 내부에서 외부로 통신 (아웃바운드) 할 수 있도록 public 서브넷에 추가하여 private 서브넷의 라우팅을 추가해줌

NAT 게이트웨이에 탄력적IP 주소 할당하여 공인 IP를 가지도록 함
````

💡 ***Bastion host와 NAT Gateway***

```` 
NAT Gateway는 내부 -> 외부 접근이 가능하다면 Bastion Host는 내부 <-> 외부 접근이 가능함
Bastion Host도 NAT Gateway와 마찬가지로 public 서브넷에 위치함
````

# EKS

Amazon Elastic Kubernetes Service, AWS에서 제공하는 kubernetes 관리형 실행 서비스

⬛ EKS 특징

1. EKS는 Kubernetes 클러스터의 Master Node를 관리
2. 다양한 서비스와 통합되어 있어 ELB, IAM 등을 통한 로드밸런싱, 권한관리 등이 가능
3. Kubernetes의 최신 버전 및 보안 패치를 자동으로 적용






