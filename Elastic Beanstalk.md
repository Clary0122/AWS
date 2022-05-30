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
