# Elastic Beanstalk Workshop
> 👉 [Workshop URL](https://catalog.us-east-1.prod.workshops.aws/workshops/3fd6c80b-39f2-4534-b69c-c400aed50c67/ko-KR/workshop-setting)

## 배포를 위한 준비
- IAM 을 사용한 역할 생성을 하는 이유
  - AWS 클라우드에서 인프라, 보안, 데이터베이스, 분석, 배포 및 모니터링 등 모든 IT 자원은 API 호출을 통해 이용된다.
    - API(application programming interface) : 요청과 응답을 사용하여 (클라이언트와 서버 사이) 두 애플리케이션이 서로 통신하는 방법을 정의한다.
  - 초기에 최소 권한 원칙에 기반하여 환경 구성하는 것을 놓치는 경우가 많고, 너무 많은 권한을 개발자에게 할당하여 사람에 의한 실수, 외부 공격으로부터 취약한 구조가 된다.
  - 필요한 정책이 할당된 역할을 위임 받은 사용자 자격증명을 통하여 AWS CLI 구성 및 AWS API 호출을 하도록하여 실제 필요한 서비스에 대한 접근만을 허용하도록 한다.
- 목표 아키텍처  
  ![image](https://user-images.githubusercontent.com/79209568/171556872-79ee9f52-03cc-47aa-8145-bb4074954d74.png)

### 애플리케이션 개발을 위한 IAM 구성
#### Role 생성
- \[Services] - \[IAM] - \[Roles] - \[Create role]
- 애플리케이션이 배포될 리소스에게만 해당 역할을 사용할 수 있음을 설정 (EC2)  
  ![image](https://user-images.githubusercontent.com/79209568/171557304-3f8e679b-5adc-40ca-838b-ddd2fdc39ca1.png)
- 관리형 정책 선택 : `SecretsManagerReadWrite`, `AWSElasticBeanstalkWebTier`  
  ![image](https://user-images.githubusercontent.com/79209568/171557525-bfa742ec-fe66-4f74-a479-a43fc84f446c.png)
  ![image](https://user-images.githubusercontent.com/79209568/171557496-8f6f648f-891b-461b-8ff0-a76e83dc5dfb.png)
- 역할 이름 : `application-role`  
  ![image](https://user-images.githubusercontent.com/79209568/171557691-0f285e1e-9c9b-4c54-9df3-0c6aef1e2490.png)
 
#### User 생성
- \[Add users]
- CLI 환경에서 개발자가 AWS에 접근할 것이기 때문에 접근 타입을 Programmatic access로 설정  
  ![image](https://user-images.githubusercontent.com/79209568/171559523-58bbc0a4-9803-4677-b117-694ff5d1533d.png)
- `Download.csv`를 클릭하여 사용자 자격증명 파일을 다운로드. 이 후에 환경 세팅에서 **Access Key, Secret Access**를 설정한다.  
  ![image](https://user-images.githubusercontent.com/79209568/171559780-dd0a409a-6cf6-4af4-85a4-748e3cd0f415.png)
- UserARN 복사  
  ![image](https://user-images.githubusercontent.com/79209568/171560998-cb313c0d-ab33-4c55-a874-b2c1fc889bd5.png)
- Roles 탭의 생성한 `application-role` 클릭, `Trust relationships` 탭의 `Edit trust policy`  
  ![image](https://user-images.githubusercontent.com/79209568/171560463-077d453c-d080-4004-91a7-46fd6960ff12.png)
- 복사해 둔 사용자 ARN을 넣어서 작성한다.  
  ![image](https://user-images.githubusercontent.com/79209568/171560788-3dfb356d-e923-4e36-9662-b78ee47655c9.png)

> #### 여기까지 **AWS 상에서 애플리케이션이 가지는 권한과 개발자가 로컬환경에서 가지는 권한**이 같아졌다.

### AWS Cloude 9으로 시작하기 or 로컬 환경으로 시작하기
> #### 로컬환경으로 시작해봤다

#### AWS CLI 설치 또는 업데이트
- [git 설치](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EC%84%A4%EC%B9%98)
- [Python설치 및 EB CLI 설치](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-install-windows.html)
- Window에서 설치 - 명령 프롬프트에서 AWS CLI 설치 명령어 입력
  ```
  msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
  ```
  ![image](https://user-images.githubusercontent.com/79209568/171564518-3b5019d0-b7a2-4ab8-893e-0b664b1fb125.png)
- AWS CLI 설치 확인
  ```
  aws --version
  ```
  ![image](https://user-images.githubusercontent.com/79209568/171564787-575bee5a-d13a-4cd2-a29c-c7c5974f6517.png)
- Windows PowerShell에서 Access Key 값 입력. 리전은 도쿄 리전
  ```
  cd ~
  aws configure
  ```
  ![image](https://user-images.githubusercontent.com/79209568/171565474-2d4e66e2-14f1-414a-8805-a179ed118ecf.png)
- 실습에서 사용할 애플리케이션 코드를 Github에서 내려받기
  ```
  git clone https://github.com/aws-samples/aws-basic-deployment-workshop-sample.git
  ```
- npm 다운로드  
  ```
  cd aws-basic-deployment-workshop-sample
  ```
  ```
  npm i
  ```
- Windows PowerShell을 **관리자 권한으로 실행**. 아래 명령어 입력하여 기존 설정된 구성 확인. (메모장 닫지 말기!)
  ```
  cd ~\.aws
  notepad credentials
  ```
- 각 파일을 작성해준다.
  ```
  notepad default-credentials.txt
  notepad set-default.ps1
  notepad workshop-credentials.txt
  notepad set-workshop.ps1
  ```
  ![image](https://user-images.githubusercontent.com/79209568/171571038-a3eba1a3-bfa1-4a08-a4f7-9329362a9ff9.png)
  - `set-default.ps1` : 실습 종류 이후, 기본 구성으로 돌아오기 위한 스크립트
  - `set-workshop.ps1` : 실습 간 사용할 자격증명으로 변경하는 스크립트
  - 생성 완료  
    ![image](https://user-images.githubusercontent.com/79209568/171571426-39b8828a-a96d-41b3-ad8a-36d39133ce1e.png)
- `.\set-workshop.ps1` 스크립트 실행
  - 오류 시 `Set-ExecutionPolicy RemoteSigned` - `Y`
    ![image](https://user-images.githubusercontent.com/79209568/171584083-2f16b05b-0d0f-4b2a-b8a0-b4c434cb6793.png)

## 네트워크 구성
> #### 목표 아키텍처
> ![image](https://user-images.githubusercontent.com/79209568/171586003-97c0e589-1963-493e-a7e8-b49bf1287199.png)










