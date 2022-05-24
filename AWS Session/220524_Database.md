# Database 
> \<INDEX>
> 1. [Purpose-built databases](##purpose-built-databases)
> 2. [Amazon Aurora](#Amazon-Aurora)
> 3. [Workshop](#Workshop)


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

### Traditional Database Architecture
- Increase I/O bandwidth
- Decrease number of I/Os consumed
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
- **peer to peer gosship**

#### read replicas are dedicated to reads
- 기존
  - 두 개의 덩어리로 동일 (Independent storage)
  - 마스터의 쓰기 워크로드가 70%고 읽기가 30%이면 replica또한 같은 워크로드 비율
  - 불필요한 워크로드 발생. 낭비

- 오로라
  - multi-az storage가 있어서 메모리 영역에서 로그?만 넘기기 때문에(page cache update) 읽기 워크로드만 100% 쓸 수 있음
  - shared storage

<hr>

### Database workload Analysis
- Refactor : rds - aurora mysql, rds-aurora postegresQL, dynamodb
- replatform : oracle zzzzzz

### Database 
- Rehost
  - lift and shift
  - smae datatbase engine and cod
- Replatform
  - change database engie
  - convet the database and 
- Rehost

## Database migragion journey
1. analysis 
2. schema objects 
3. data migration 
4. application code 
5. testing : 대부분의 시간이 필요
6. cutover : 가장 적은 시간 필요

### Resources and skills required
- Database expertise is requiired : Working knowlege of target database engine
- Basic networking knowledge : Familiarity with AWS VPC concepts
- AWS knowledge is reqired
- Software architecture knowlege is an advantage

### Understand your network
- 데이터센터를 클라우드로 마이그레이션 하는 것이기 때문에 대역폭이 달라짐
- 돈을 써서 빠르게 하는 것인지, 적당한 돈으로 적당히 빠른 속도로 하는 것인지 결정해야 함

### 고객 요구사항
- downtime 얼마나? 등
- 다운타임은 적었으면 좋겠지만 데이터 양이 많고 대역폭이 좁은 상태면 고객과의 협의 필요

### Create your target schema using AWS services
#### DMS
- 단순 데이터 전환이 아닌 CDC 방식으로 스트림 형식. 아파치 카프카? 각 디비 별로 커낵터로 변경사항을 캡쳐하고 내부에 있는 카프카를 통해 큐를 쏴서 어플리케이션 내부에서 어디로 데이터를 보낼지 결정
- CDC 솔루션 : 데이터베이스 내에 변경되는 내용들을 엔진이 다른 DB에 쿼리로 적용할 수 있는 솔루션 (각 DB 별로 다른 로그 기록 형식을 분석해서 다른 DB의 쿼리로 적용시킨다.)
- 장점
  - 설정이 간단
  - DB 별 사전에 설정하는 것이 필요하긴 하지만 DMS를 사용하기 위한 설정은 필요없음
  - 마이그레이션, 혹은 CDC 솔루션에 사용되는 서비스
#### SCT (Schema Conversion Tool)
- For heterogeneous migrations.
- 데이터베이스 종류가 다르게 마이그레이션 하는 경우 문제없이 전환할 수 있을지 검증하여 레포팅 해주는 툴
- 이런 컬럼은 변경이 불가. 이런 컬럼은 변경 가능하지만 설정 필요. 이런식으로
- 마이그레이션의 복잡성, 공수? 확인할 수 있는 툴

### Replication instance
- dedicated ec2 instance
- public or privvate IP address
- Task execution  

### Migration Task
- Replication instance에 많은 task를 올릴지, Replication instance의 수를 늘려서 task를 나눌지 성능에 따라 결정


<hr>

# Workshop
## Oracle to Amazon Aurora (PostgreSQL)
> [**Workshop URL**](https://catalog.us-east-1.prod.workshops.aws/workshops/77bdff4f-2d9e-4d68-99ba-248ea95b3aca/en-US)

![image](https://user-images.githubusercontent.com/79209568/169954920-89d9a414-2e2b-4b3e-8690-b03993f0a7c3.png)

![image](https://user-images.githubusercontent.com/79209568/169959138-61a9b4e7-23bb-4476-8f79-e092e8985b41.png)

![image](https://user-images.githubusercontent.com/79209568/169959425-04ddd8ce-6408-48b3-b9bb-892104d2f20d.png)

![image](https://user-images.githubusercontent.com/79209568/169987464-b9cb6044-51d9-437e-b96b-b2f838a05586.png)

![image](https://user-images.githubusercontent.com/79209568/169982586-536f59d1-788d-4a9f-808a-3f4a0cfe8f31.png)

![image](https://user-images.githubusercontent.com/79209568/169983086-61ac1faa-7a0b-4a3d-a7b2-3f4c24a421c2.png)

![image](https://user-images.githubusercontent.com/79209568/169985685-020d73f2-c5cb-4a76-b280-20d9bf8c3fb5.png)
