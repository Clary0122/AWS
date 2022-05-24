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
> MySQL, PostgreSQL에 호환되는 관계형 데이테베이스, 상용 DB 비용보다 1/10비용
```
분산 스토리지 제공 
Redo log processing
Instant crash recovery
fault-tolerant and self-healing stroage
fast database cloning
database backtrack
database snapshots
continuous backups and point-in-time restore
storage automatic scaling independent of compute
read and write scalability
wwarm cache on database
```
### Quick recap 
- Do-REDO-UNDO protocol
  - DO 사용자의 작업
  - REDO : 사용자의 작업으로 생긴 data와 로그를 통해 REDO
  - UNDO : 되돌아가기
- Crash Recovery

### Traditional Dtatbase Architecture
?
### Aurora approach 
#### Log is the database
- 로그를 기반으로 데이터 베이스에 대한 데이터 정합성 보장
- 원본 데이터가 t0밖에 없지만 로그가 t1~t5까지 있으면 데이터 영역에 내려 쓸 수 있다. 
- 정통적인 DB 보다 데이터 영역에 내려 쓰는 것을 늦게 함
- 로그 데이터로 충분하다는 것
#### offload checkpointing to the stroage fleet
- 문제 1 : 변경사항이 며칠 이상 씩 누적됐는데 DB 죽었다가 살았을 시기에 모두 적용시킨다면 너무 오래 걸린다
  - 솔루션 : 주기적으로 체크 포인트를 생성한다.
- 문제 2 : 체크포인트에서 부하가 일어난다.
  - 솔루션 : 스토리지 fleet에서 관리 중
### Compute and Storate Seperation
multi tenant, multi attach 실제 데이터가 다른 서비스 노드(읽기 모드)로 존재하는 것이 아니라 이 데이터 노드를 여러.. 데이터 복제가 필요 없음, purpose built(서비스를 구현하는 것에 purpost built)



#### Scale-out distributed, multi tenant storage architecture
- 사용자가 보이는 Cluster storage volume - PG(protect group) 3개 , 3개의 az에 각각의 PG가 나눠져서 저장 됨(여긴 사용자 보이지 않음) 
- 큐롬?

#### I/O flow in amazon aurora storage node
- 로그가 스토리지에 들어가서 핫 로그로 들어가서 잘 들어갔다고 디비 인스턴스에 ACK 커맨드를 날림(싱크로 진행)
- oraganize records and identify gaps in log
- gossip with peers to fill in holes
- **peer yo peer gosship**

#### read replicas are dedicated to reads
- 기존
  - 두 개의 덩어리로 동일 (Independent storage)
  - 마스터의 쓰기 워크로드가 70%고 읽기가 30%이면 replica또한 같은 워크로드 비율
  - 불필요한 워크로드 발생. 낭비

- 오로라
  - multi-az storage가 있어서 메모리 영역에서 로그?만 넘기기 때문에(page cache update) 읽기 워크로드만 100% 쓸 수 있음
  - shared storage















