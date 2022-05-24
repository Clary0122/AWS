# Database 
> \<INDEX>
> 1. [Purpose-built databases](##purpose-built-databases)
> 2. [Amazon Aurora](#Amazon-Aurora)


## Purpose-built databases
### App architectures have evolved over the years 
(Mainframe - client server - three tier - micro service)
### Top of mind for our customer
- Move to managed : 관리를 편하게 하는 managed service
  - Services : RDS, Aurora ElastiCache, DocDB, ElasticSearch
  - Tools : SCT, DMS
  - Programs: MAP, Proserve, Partner
- Break Free : 비용에 대한 부담, 상용 소프트웨어를 피하는 경향(오픈 소스 사용)
  - Service : Aurora, Amazon Redshift
  - Tool : SCT, DMS
  - Programs : MAP, DB Freedom, Proserve, Partner
- New modern Apps : 어플리케이션은 기존에 웹에서만 했지만 모바일 등에서 개발되고 있기 때문에 새로운 형태의 데이터들이 많아짐 
  - Client - Internet- CF -APIgateway lambda s3 - dynamDB, elastiCache - Aurora - 목적에 맞게 쪼개서 관리

### Purposebuilt Databases
|**DB 유형**|**사용사례**|**AWS 서비스**|
|--|---|--|
|relational|기존 애플리케이션, ERP, CRM, 전자 상거래|Aurora, RDS, Redshift|
|key-value| - 높은 트래픽의 웹 앱, 전자 상거래 시스템, 게임 애플리케이션<br>- 저장된 데이터의 유연한 접근(db내에서 집계 진행 X)|DynamoDB|
|document|콘텐츠 관리, 카탈로그, 사용자 프로필|DocumentDB(MongoDB 호환)|
|in-memory|- 캐싱, 세션 관리, 게임 순위표, 지리 공간 애플리케이션<br>- 사용자의 세션정보가 있어서 어플리케이션내애서 서버가 끊겨도 중간에 메모리 용도로 사용|Elasticache|
|graph|- 부정 탐지, 소셜 네트워킹, 추천 엔진<br>- 사용자의 관계 데이터 (관계분석을 위한 데이터베이스)|Neptune|
|time-series|- IoT 애플리케이션, DevOps, 산업용 텔레메트리<br>- 시계열 데이터 |Timestream|
|ledger|레코드 시스템, 공급망, 등록, 은행 거래|QLDB|
|wide column |장비 관리, 플릿 관리 및 경로 최적화에 사용하는 대규모 산업용 앱|managed cassandra, Keyspaces|

## Amazon Aurora
