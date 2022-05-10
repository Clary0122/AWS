# SAP 기반 분석 플랫폼 구축
## AWS 위 SAP 분석 플랫폼을 만들어야 하는 이유
- 이전 : 데이터 사일로 딜레마
  - 데이터가 전체적으로 통합되지 않고 개별 부서나 사업 부문별로 고립적으로 활용하는 현상
- 데이터레이크 아키텍처 도입
  - 발생하는 모든 데이터를 하나의 클라우드 중앙 레포지토리에 저장하는 데이터레이크 아키텍처가 생겨남
  - 데이터레이크 : 대규모의 다양한 원시 데이터 세트를 기본 형식으로 저장하는 데이터 리포지토리 유형 (정제되지 않음)
  - Amazon **S3**를 많이 사용
- SAP 데이터 추출
  - **어플리케이션 레벨 추출** : RFC(원격호출방법), oData 활용 -> 호출하기 위한 개발 및 솔루션이 필요하지만 SAP가 제공하는 데이터뷰를 그대로 유지 유지 가능   
    > oData : Restful API로 Data 전송용 프로토콜 
    - AWS Glue(탐색, 추출), AWS Lambda(이벤트 트리거)
      - Glue : 서버리스 데이터 통합 서비스. 데이터를 쉽게 탐색, 준비, 조합 지원
      - Lambda : 이벤트 중심의 서버리스 컴퓨팅 서비스.  
    - 둘 다 자동코드 생성 및 템플릿을 제공하지만 SAP oData 연결하는 부분이나 필요한 변환작업에는 **추가 개발**이 필요
    - 이것을 지원하기 위해 **AppFlow**에 새로운 기능 출시
  - 데이터베이스 레벨 추출 : 파트너 솔루션 및 DBMS에서 제공하는 DB 복제 기술 이용 -> 간단하지만 파트너 솔루션 도입을 위한 비용과 복제르르 위한 추가 DB license가 필요
## AppFlow
![image](https://user-images.githubusercontent.com/79209568/167689499-03980091-bad9-4d32-aa03-295e3755f90d.png)

- 클릭 몇 번으로 타사 어플리케이션(Salesforce, Slack, SAP 등)과 AWS 서비스(S3, Redshift) 간에 데이터를 안전하게 전송할 수 있게 해주는 완전관리형 통합 서비스
- 필터링 및 검증과 같은 데이터 변환 기능 구성.
- 추가 단계 없이 바로 사용가능한 풍부한 데이터 생성 가능
- 새로운 기능 : SAP oData Connector
## AppFlow 보안 네트워크 구축 - AWS PrivateLink
- SAP ERP 및 BW에 저장되어 있는 데이터는 비즈니스 핵심 데이터이기 때문에 **VPC 혹은 온프레미스 내 private network** 안에 SAP Application을 구축
- 이러한 보안 네트워크 환경을 지원하기 위해 **AWS PrivateLink**를 이용  

![image](https://user-images.githubusercontent.com/79209568/167690472-0f81b132-33fc-40ce-bd99-507e85dae556.png)

1. AppFlow가 **PrivateLink와 Network Load Balancer**를 통해 VPC 내부에 접근
2. 경우에 따라서는 VPC및 AWS Account가 분리되어 있을 수 있음 → 이럴 때는 **AWS Transit Gateway**의 peering기능을 이용해서 다른 account에 접속 가능
3. 온프레미스에 sap 애플리케이션이 있는 경우 → VPN 혹은 Direct Connect를 이용하여 연결

## 데이터 준비 작업 - AWS Glue DataBrew
- SAP 어플리케이션에서의 데이터 말고도 다른 어플리케이션 및 여러 데이터가 함께 저장되는 경우가 많음
- 그럴 경우 데이터를 결합하거나 삭제 및 정규화 작업 등이 필요
- 변환 작업 외의 개발이 필요함. → Glue, Lambda로 가능
- 하지만 데이터 변환을 위해 Python, Pyspark 개발 작업이 익숙하지 않은 고객을 위한 **Glue DataBrew 서비스**
- **AWS Glue DateBrew** : Glue를 위한 새로운 시각적 데이터 준비 도구. 코드 작업 없이 데이터를 정리 및 정규화 (데이터 준비 시간 최대 80% 절약 가능)

# 전체 플로우
![image](https://user-images.githubusercontent.com/79209568/167692403-d2e3863d-7000-4103-8e70-8c5d941e82f3.png)
