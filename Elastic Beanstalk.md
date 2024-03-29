# Elastic Beanstalk
## Elastic Beanstalk란?
- 애플리케이션을 실행하는 인프라에 대해 자세히 알지 못해도 AWS 클라우드에 애플리케이션을 신속하게 배포하고 관리 가능
- 관리 복잡성 ↓ : 애플리케이션 업로드만 하면 알아서 **용량 프로비저닝, 로드 밸런싱, 조정 애플리케이션 상태 모니터링**에 대한 세부 정보를 자동으로 처리
- Go, Java, NET, Nods.js, PHP, Python, Ruby로 개발한 애플리케이션 지원
- 배포할 때, EB는 선택된 플랫폼 버전을 구축하고 EC2 등의 AWS 리소스를 하나 이상 프로비저닝하여 애플리케이션을 실행
- 상호작용 방법 : EB 콘솔, AWS CLI, eb(EB를 위해 특별히 설계된 고급 CLI)
- EB 콘솔에서 직접 EC2 인스턴스 플릿 크기 변경 가능, 모니터링 가능

### 시용방법
![image](https://user-images.githubusercontent.com/79209568/170908377-2e69ad46-59e7-4ab2-b5c8-4e5887ce7b95.png)
#### 1. Create Application
#### 2. Upload Version
- 애플리케이션 소스 번들 형태 (예 자바의 경우 .war 파일)로 애플리케이션 버전을 업로드한다.
- 애플리케이션에 대한 몇 가지 정보를 제공
#### 3. Launch Environment
- 자동으로 환경을 실행하고 코드 실행에 필요한 AWS 리소스를 생성 및 구성
#### 4. Manage Environment
- 환경 실행 후 EB가 환경을 직접 관리
- 새로운 앱 법전 배포 가능

### 요금
- EB에 대한 추가 비용은 없지만 애플리케이션에서 사용할 **기본 AWS 리소스에 대한 비용은 필요**

## 주요 개념
|개념|설명|
|--|----|
|애플리케이션|- 환경, 버전 및 환경구성을 포함한 EB 구성 요소의 논리적 컬렉션.<br>- 개념적으로 폴더와 유사|
|애플리케이션 버전|- Java WAR 파일 등의 배포 가능한 코드가 포함된 S3 객체를 가리킨다.<br>- 애플리케이션의 일부. 애플리케이션 내에 많은 버전이 있을 수 있지만 각 애플리케이션 버전은 고유한 이름을 가져야한다.<br>- 실행 중인 환경에서 버전을 업로드하고 즉시 배포 가능<br>- 여러 버전을 업로드하여 한 웹 애플리케이션 버전과 다른 버전 간의 차이 테스트 가능.|
|환경|- 애플리케이션 버전을 실행 중인 AWS 리소스 모음.<br>- 각 환경에서 한번에 하나의 애플리케이션 버전만 실행 가능.<br>- 환경을 생성하면 EB에서 사용자가 지정한 애플리케이션 버전을 실행하는 데 필요한 리소스를 프로비저닝 함.|
|환경 티어|- 환경을 생성할 때 환경티어를 선택한다. <br>- 환경에서 실행하는 애플리케이션 유형 지정, 프로비저닝하는 리소스 결정.<br>- HTTP 요청을 처리하는 애플리케이션은 [**웹 서버 환경 티어**](#웹-서버-환경), Amazon SQS 대기열에서 작업을 가져오는 백엔드 환경은 [**작업자 환경 티어**](#작업자-환경)에서 실행|
|플랫폼|운영체제(OS), 프로그래밍 언어 런타임, 웹 서버, 애플리케이션 서버 및 EB 구성 요성의 조합|

### 환경 티어
#### 웹 서버 환경
![image](https://user-images.githubusercontent.com/79209568/171077716-c66311dd-064c-4d2c-8068-fc31406366c5.png)
<아키텍처 예제>  
- 환경 생성 시 EB는 애플리케이션을 실행하는 데 필요한 리소스를 프로비저닝한다. (하나 이상의 ELB, Auto Scaling 그룹, EC2)
- 로드 밸런서를 가리키는 CNAME(URL)이 있다. (Myapp.elasticbeanstalk.com) 이 URL은 Route 53을 통해 ELB의 URL로 별칭 지정 가능. (Myelb.elb.amazoneaws.com)
- Auto Scaling이 로드 증가 감소에 따라 EC2를 중지 실행한다. 하지만 최소 한 개의 인스턴스는 실행 상태로 둔다.
- 각 인스턴스에는 호스트관리자(HM)라는 소프트웨어 구성 요소가 실행된다.
  - 호스트 관리자
    - 애플리케이션 배포
    - 콘솔, API, CLI을 통해 검색을 위해 이벤트와 측정치 집계
    - 인스턴스 수준 이벤트 생성
    - 애플리케이션 로그 파일을 모니터링하여 심각한 오류 검출
    - 애플리케이션 서버 모니터링
    - 인스턴스 구성 요소 패칭
    - 애플리케이션의 로그 파일을 교체하고 이를 S2에 게시
- 보안 그룹을 통해 인스턴스의 방화벽 규칙을 정의한다. 기본적으로 EB가 보안 그룹을 정의하며 이를 통해 80 포트를 사용하여 사용자가 연결할 수 있다.
- 보안 그룹을 두 개 이상 정의한다. 예를 들어 DB 서버와 EC2 서버
#### 작업자 환경
![image](https://user-images.githubusercontent.com/79209568/171080622-afdc638f-adb2-41e8-9168-a33227ac75f3.png)
<아키텍처 예제>
- 환경 생성 시 프로비저닝 되는 리소스들은 Auto Scaling 그룹, 하나 이상의 EC2, IAM 역할, SQS이다.
- Amazon SQS 대기열이 아직 없으면 EB가 만들어서 프로비저닝 한다.
- 작업자 환경을 시작하면 선택한 프로그래밍 언어에 필요한 지원 파일과 데몬을 각 EC2에 설치한다. 
- 데몬이 SQS 대기열로부터의 메시지를 읽은 후 처리를 위해 각 메시지의 데이터를 실행 중인 웹 어플리케이션에 보낸다. 
- 여러 개의 인스턴스가 있는 경우 각 인스턴스의 고유의 데몬이 있지만 모두 동일한 SQS 대기열을 읽는다.
## 설계 고려 사항
### 확장성
- Auto Scaling 설정을 통해 서버 사용률 또는 네트워크 I/O 지표로 자동으로 EC2 추가, 제거할 수 있도록 가능하다.
- 시스템 지표를 CloudWatch에 게시한 후 Auto Scaling 작업을 트리거하거나 알림 전송으로 경보를 구성할 수 있다.
- EB의 모든 애플리케이션은 필요에 따라 확장할 수 있는 느슨하게 결합된 내결함성 구성요소를 사용하며 가급적 상태 비저장으로 설계하는 것이 좋다.
### 보안
- 애플리케이션과 클라이언트 사이에 흐르는 정보를 보호하기 위해 SSL을 구성한다.
- Amazon ACM, CA(외부인증기관)인증서 등
- 환경에 SSL 인증서를 구성하면 클라이언트와 환경의 ELB 간에 데이터가 암호화된다. 이 암호화는 기본적으로 ELB에서 종료되고 ELB와 EC2 인스턴스 간의 트래픽은 암호화되지 않는다.
### 영구 스토리지
- EB 애플리케이션은 영구 로컬 스토리지가 없는 EC2 인스턴스에서 실행되므로 종료되면 로컬 파일 시스템이 저장되지 않는다. 
- 영구 스토리지에 데이터를 저장하도록 애플리케이션을 구성하는 것이 좋다. (S3, File System, DynamoDB, RDS)
- Amazon Linux 2 플랫폼 버전의 경우, EB는 웹앱 사용자에게 새로운 환경에 대한 uid(사용자 ID) 및 gid(그룹 ID) 값 **900**을 할당한다. 이 웹앱 사용자는 플랫폼 업데이트에도 영구 파일 시스템 스토리지에 대한 일관된 액세스 허가를 유지할 수 있다. 만약 이미 다른 사용자나 프로세스가 900을 사용 중이면 운영체제가 알아서 다른 기본 값을 준다. (`id webapp`으로 할당 된 uid, gid 확인 가능)
### 내결함성
- 항상 장애 시 자동 복구할 수 있도록 설계, 구현 및 배포한다. 
  - 여러 가용 영역에 EC2, RDS를 둔다.
  - CloudWatch를 사용하여 EB 애플리케이션 상태를 쉽게 확인하여 하드웨어 장애나 성능 저하 시 적절한 조치를 취한다.
  - 비정상 EC2를 새 인스턴스로 바꾸도록 Auto Scaling 구성을 해 놔서 EC2 인스턴스를 일정한 크기로 유지한다.
  - RDS를 사용하면 자동 백업을 수행하도록 백업의 보존 기간을 설정한다.
### 콘텐츠 전송
- CloudFront 사용
### 소프트웨어 업데이트 및 패칭
- EB는 새 플렛폼 버전을 정기적으로 릴리스하여 모든 Linux 기반 및 Windows Server 기반 플랫폼을 업데이트한다. 
- EB 콘솔 또는 EB CLI를 사용하여 환경ㅇ의 플랫폼 버전을 업데이트 할 수 있다. 업데이트 대상 플랫폼 버전에 따라 업데이트를 수행하는 두 가지 방법이 있다.
- [환경의 플랫폼 버전 업데이트](#플랫폼-업데이트)
### 연결성
- 배포를 완료하려면 EB를 환경의 인스턴스에 연결해야 한다. VPC 내부에 EB 애플리케이션을 배포하는 경우, 연결성을 지원한다. 필요한 구성은 생성하는 VPC 환경의 유형에 따라 다르다.
- 단일 인스턴스 환경의 경우
  - 추가 구성 필요 없음.
  - EB가 각 EC2 인스턴스에 퍼블릭 EIP 주소를 할당하여 인스턴스가 인터넷과 직접 통신할 수 있기 때문에
- public, private 서브넷이 모두 있는 VPC의 로드밸런싱 수행 및 확장 가능 환경의 경우
  - 인터넷에서 EC2로 인바운드 트래픽을 라우팅할 수 있도록 public 서브넷에 로드 밸런서 생성
  - private 서브넷의 EC2에서 인터넷으로 아웃바운드 트래픽을 라우팅할 수 있도록 네트워크 주소 변환(NAT) 디바이스 생성
  - private 서브넷 내부에 EC2 인바운드 및 아웃바운드 규칙 생성
  - NAT 인스턴스를 사용하는 경우, 인터넷 통신을 사용하도록 NAT 인스턴스와 EC2 인스턴스의 보안 그룹을 구성

## 권한
- 환경 생성 시 AWS Identity and Access management(IAM) 역할을 제공하라는 메시지가 EB에 표시 됨.
- **서비스 역할** : 사용자를 대신해 다른 AWS 서비스를 EB가 사용하도록 역할을 수임
- **인스턴스 프로파일** : EB가 환경 내의 인스턴스에 프로파일을 적용하여 S3에서 애플리케이션 버전 검색, S3에 로그 업로드, 환경 유형 및 플랫폼에 따라 다른 작업을 수행한다.
- 서비스 역할과 인스턴스 프로파일은 EB 콘솔과 EB CLI를 통해 환경을 생성할 때 자동으로 생성한다.
- 두 역할 외에 **사용자 정책**을 만들어서 게정에 속한 IAM 사용자 및 그룹에 적용 가능

## 플랫폼
- EB는 사용자가 선택한 플랫폼 버전에 코드를 배포하여 활성 애플리케이션 환경을 생성한다.
- 플랫폼은 운영체제(OS), 런타임, 웹 서버, 애플리케이션 서버 및 EB 구성 요소(EB가 EB 기능을 활성화하기 위해 플랫폼에 추가하는 소프트웨어 구성 요소)의 조합
### 용어 정리
#### 런타임
- 애플리케이션 코드를 실행하는 데 필요한 프로그래밍 언어별 런타임 소프트웨어(프레임워크, 라이브러리, 인터프리터, VM 등)
#### Elastic Beanstalk 구성 요소
- EB가 EB 기능을 활성화하기 위해 플랫폼에 추가하는 소프트웨어 구성요소.
#### 플랫폼 버전 
- **특정 버전**의 플랫폼. 플랫폼 버전에 기반한 EB환경을 만들고 애플리케이션을 이 환경에 배포
- 형태 : *X.Y.Z* (메이저.마이너.패치)
  ```
  예) 8.2.72 버전
    메이저 버전 : 8
    마이너 버전 : 2
    패치 버전 : 72
  ```
- 상태
  - 지원 상태 : 지원되는 구성요소로 완전히 구성되는 플랫폼 버전. 
  - 만료 상태 : 만료되 구성요소가 하나 이상 포함된 플랫폼 버전. 새 고객이든 기존 고객이든 EB 환경에서 만료된 플랫폼 버전은 사용할 수 없다.

#### 플랫폼 브랜치
```
Github의 브랜치 개념과 같음.
특정 지점으로 돌아갈 수 있음
```
- 운영체제(OS), 런타임 또는 EB구성요소와 같은 일부 구성요소의 특정 버전을 공유하는 플랫폼 버전. 
- 현재 환경에서 만료된 플랫폼 브랜치를 사용하는 경우 지원되는 플랫폼 브랜치의 플랫폼 버전으로 업데이트 하는 것이 좋다.
   - **지원** 상태 : 현재 플랫폼 브랜치. 전체저긍로 지원되는 구성 요소로 구성. 플랫폼 업데이트를 지속적으로 받으며 프로덕션 환경에서 사용하는 것이 좋음.
   - **베타** 상태 : 미리보기, 시험판 플랫폼 브랜치. 잠시 동안 지속적 플랫폼 업데이트를 받을 수 있지만 장기적인 지원은 없음. 프로덕션 환경에서 사용하지 않는 것이 좋음.
   - **사용 중단** 상태 : 사용되지 않는 구성요소가 하나 이상 포함된 플랫폼 브랜치. 플랫폼 업데이트를 지속적으로 받지만 프로덕션 환경에서 사용하지 않는 것이 좋음.
   - **만료** 상태 : 만료된 구성요소가 하나 이상 포함된 브랜치. 플랫폼 업데이트를 더이상 지원하지 않으며 프로덕션 환경에서 사용하지 않는 것이 좋음.
#### 플랫폼 업데이트
- 플랫폼의 일부 구성요소에 대한 업데이트를 포함하는 새 플랫폼 버전의 릴리즈
- **메이저** 업데이트
  - 이전 플랫폼 버전과 호환되지 않는 변경 사항이 있는 업데이트.
  - 새 메이저 버전에서 올바르게 실행하려면 애플리케이션의 수정이 필요할 수 있음
- **마이너** 업데이트
  - 이전 플랫폼 버전과 호환되는 기능을 추가하는 업데이트.
  - 애플리케이션을 수정하지 않아도 새 마이너 버전에서 올바르게 실행할 수 있음.
- **패치** 업데이트
  - 이전 플랫폼 버전과 호환되는 유지 관리 릴리스(버그 수정, 보안 업데이트 및 성능 개선)로 구성되는 업데이트.

#### 관리형 업데이트
- 운영체제(OS), 런타임, 웹 서버, 애플리케이션 서버 및 EB가 지원되는 플랫폼 버전을 위한 EB 구성 요소에 패치 및 마이너 업데이트를 **자동으로 적용**하는 기능
- `패치 업데이트만` 또는 `마이너 및 패치 업데이트` 적용할 수 있도록 관리형 업데이트 구성 가능.
- 관리형 업데이트 완전히 `비활성`도 가능

### 플랫폼 유지관리 공동 책임 모델
- AWS와 고객은 AWS의 공동 책임 모델을 통해 운영 부담을 나눈다.
  - AWS의 책임 '클라우드의 보안' : AWS에서 제공되는 모든 서비스를 실행하는 인프라를 보호할 책임이 있음. 하드웨어, 소프트웨어, 네트워킹 
  - 고객의 책임 '클라우드에서의 보안' : 운영체제 관리(업데이트, 보안 패치 등), 인스턴스에 설치한 모든 애플리케이션 소프트웨어, 유틸리티 관리, 방화벽 구성 관리
- EB는 **관리형 업데이트 기능**을 제공하여 공동 책임 모델에 있어서 본인의 의무를 수행할 수 있듬. 이 기능을 통해 플랫폼 버전에 대한 패치 및 마이너 업데이트를 자동으로 적용한다. 관리형 업데이트를 실패한 경우 사용자가 상황을 파악하여 즉각적인 조치를 취할 수 있도록 EB에서 실패사실을 알린다.

### 플랫폼 지원 정책
- AWS에서 애플리케이션을 실행할 수 있는 다양한 플랫폼을 지원
- 공급업체로부터 지속적인 마이너 업데이트, 패치 업데이트를 받고 있는 플랫폼 브랜치를 지원
- 지원 되던 플랫폼 브랜치의 구성요소를 공급업체가 수명종료(EOL)로 표시한 경우, EB가 해당 플랫폼 브랜치를 만료로 표시.
  - 만료 플랫폼은 기존 및 신규 EB 고객 모두 해당 플랫폼 버전을 새 환경에 대한 배포에 사용할 수 없음.
  - 만료된 플랫폼 브랜치는 만료 날짜로부터 90일 동안 기존 고객 환경에서 사용할 수 있음
- 90일을 넘겨 만료된 플랫폼 버전에서 EB 환경을 실행하는 기존 고객의 경우, EB에서 EB 구성요소를 자동으로 제거. 실행 중인 애플리케이션 및 연결된 AWS 리소스에 대한 지속적인 관리 및 지원 책임을 고객에게 이관.

## 애플리케이션 관리
### 애플리케이션 버전 관리
- EB는 소스코드가 업로드 될 때마다 애플리케이션 버전을 생성.
- 일반적으로 콘솔 또는 EB CLI를 사용하여 환경을 생성하거나 코드를 업로드 및 배포하는 경우 애플리케이션 버전이 생성된다.
- 버전 삭제는 콘솔 또는 EB CLI를 통해 사용자가 직접 할 수도 있고, 애플리케이션의 수명 주기 정책 혹은 애플리케이션이 삭제될 때 버전이 삭제된다.

#### 버전 수명 주기
- 새 버전은 계속해서 생성되는데 더 이상 사용하지 않는 버전을 삭제하지 않으면 **애플리케이션 버전 할당량**에 도달하여 새 버전을 생성할 수 없게 된다.  
  ![image](https://user-images.githubusercontent.com/79209568/171536748-4ca56d9b-c05c-45c5-be98-105f5fe213c6.png)
- 오래된 애플리케이션 버전을 직접 삭제하거나 애플리케이션 총 버전 수가 지정된 수를 초과하면 애플리케이션 버전을 삭제하라고 EB에 알려준다.
- EB는 새 애플리케이션 버전을 생성할 때마다 애플리케이션 수명 주기 정책을 적용하고, 적용될 때마다 최대 100개의 버전을 삭제한다.
- 삭제 될 버전에는 현재 버전이 포함 되지 않는다. 또한 현재 버전 또는 정책이 트리거되기 전 10이내 종료된 환경에 배포된 애플리케이션 버전도 삭제하지 않는다.
- 기본저그올 EB는 데이터 손실을 방지하기 위해 S3에 애플리케이션 버전의 **소스 번들**(단일 zip 파일 또는 war 파일)을 남겨둔다.
- **콘솔**에서 애플리케이션 수명 주기를 설정한다.
  - 애플리케이션 버전의 총 개수 또는 애플리케이션 버전의 사용 기간을 기준으로 수명 주기 설정을 구성
  - 버전이 삭제되는 경우 S3의 소스 번들을 삭제할지 여부 지정
  - 버전을 삭제할 서비스 역할을 지정. 버전 삭제에 필요한 모든 권한을 포함하려면 기본 EB 서비스 역할인 `aws-elasticbeanstalk-service-role`을 선택. 혹은 EB 관리형 서비스 정책을 사용하는 다른 서비스 역할을 선택

#### 애플리케이션 버전 태그 지정
- 애플리케이션 버전에 태그를 지정할 수 있다.

### 리소스에 태그 지정
- EB 애플리케이션에 태그를 지정할 수 있다. 각 애플리케이션에 최대 50개의 태그를 추가할 수 있다.

<hr>

- [Elastic Beanstalk 워크샵 진행 정리]()  
- [Elastic Beanstalk 워크샵 URL](https://catalog.us-east-1.prod.workshops.aws/workshops/3fd6c80b-39f2-4534-b69c-c400aed50c67/ko-KR/workshop-setting)]
