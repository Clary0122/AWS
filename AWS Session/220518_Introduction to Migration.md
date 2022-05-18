> \<INDEX>
> - [Journey to Cloud](#Journey-to-Cloud)
> - [역량 관점으로 보는 클라우드 Assess 방법론](#역량-관점으로-보는-클라우드-Assess-방법론)
> - [성공적인 migration + modernization](#성공적인-migration-modernization)
> - [AWS All-in migration 소개](#AWS-All-in-migration-소개)

# Journey to Cloud
## 1. Project
- 클라우드 프로젝트 단위로 시작. 많은 규모의 클라우드를 만들어볼까?
## 2. foundation 
- 매우중요. 많은 사람들이 거버넌스 없이 마이그레이션을 진행하기 때문에 
- 클라우드 효용성 확인 후 클라우드 도입 확정을 위한 기초 투자 집행
- 거버넌스가 잘 잡힌 기업 : 두산
- Single Treaded leader + Leadership Team Alignment 중요
- 불명확한 Cloud Migration Goal 정의가 중요
- Create Consensus : 고객 비용 최적화, 비즈니스 요건에 맞게 설계해서 잘 사용할 수 있을지 고민해야함
- Establish principles
  - 목적과 원칙을 가지고 마이그레이션을 진행한다는 것을 지정해놓아야 함
- CCoE(Cloud Centre of Excellence)
  - 대형 마이그레이션에서 CCoE를 만들어야함
  - 기업의 클라우드 여정을 리드하고 지원하는 클라우드 전문성 보유 조직
  - CBO(Cloud Business Office; 돈 관련, 경영진 요구사항 충족), CPE(Cloud Platform Engineering; 아키텍처, 보안 담당)
    - CPE : 전사 표준을 패키지화하고 지속적으로 향상시켜 클라우드 서비스를 self-servie화 하여 클라우드 사용자들이 손쉽게 사용할 수 있도록 함
- Bring in a Partner
- Train, Gain : 기능 자격증을 따자
- 플랜을 그 때 상황에 맞춰서 원칙을 가지고 바꿀 수 있어야 함
- 7 R App migration patterns
  - 현 IT 환경 : IT 인프라스터럭처에만 집중하는 경향이 높음 -> **app**에 대한 부분을 봐야 함
## 3. migration
- mission critical 마이그레이션 포함 클라우드 전환 진행

## 4. Reinvention
- 클라우드 도입을 통해 확보한 역량(비즈니스 및 테크놀로지 최적화) 기반 비즈니스 혁신
## 5. disrupt
- 클라우드 마이그레이션 완료, 신규 어플리케이션 클라우드 네이티브 개발, 클라우드 운영 자동화

## 클라우드 여정을 통해서 확보 가능한 비즈니스 가치
> 중요! 외워두자
- **비용절감** : 절감은 되지만 많은 노력과 기간이 필요
- **업무 생산성** : 인프라, 어플리케이션 관점에 따라 다르게 실현
- **운영 탄력성** : 재해 복구를 고려, 주요 업무를 multi az 및 multi 리전에서 운영
- **비즈니스 민첩성** : 개발 및 출시가 매우 빠름

> Keyword : 7R, 목적 & 원칙, 클라우드 가치(비용절감, 업무생산성, 운영탄력성, 비즈니스민첩성)


# 역량 관점으로 보는 클라우드 Assess 방법론
## Level of Outcome
- 다섯가지 카테고리
  - Ativity : 그냥 구축
  - Technology Capability
  - Technology Benefit
  - Business capability
  - Business Benefits
- AWS CBO : Biz IT, CXO 와의 Alignment 부족으로 인하여 단순 기능 구현 혹은 기술 역량 확보 수준으로 과제가 수행되는 경우 발생 -> AWS 서비스를 통해 outcome 은 단지 기능 구현을 넘어서 기술 역량 확보 더 나아가 비즈니스 역량 및 비즈니스 베네핏을 실현하는 것을 지향

## Cloud Adoption Framework
- AWS에서 수행하는 모든 사업 = CAF
- 보장되지 않는 클라우드 도입 :클라우드 교착상태, polot stall, cloud chaos ,??
- AWS 경험과 best practice를 기반으로 고객의 digital transformation을 지원. 또한 고객의 innovative한 aws서비스를 사용을 통해서 비즈니스 아웃컴 확보를 가속화 지원
- **6가지 영역** : 클라우드를 도입하기 위해 준비해야할 영역, 고객들이 잘 잡고 가는지 확인을해야 함
  - **business**(1위)
  - **people**(2위) : 사람,조직 디자인(기존의 온프레미스 조직과 똑같으면 안됨)
  - **governace**(5위)
  - **platform**(3위)
  - **security**(4위)
  - **operations**(6위)
  - 프로젝트 중 고객사가 가장 문제를 많이 느끼는 순위 : 비지니스, 사람 문제를 우리도 알고 고민해봐야함

### AWS 방법론 비교 : CMA, CAF, MRA
- 6가지의 영역을 가지고 **진단**을 해본다

#### CMA
- 마이그레이션 프로젝트를 문제없이 진행할 수 있는지 6가지 영역의 히트맵으로 확인 가능한 진단 도구

#### MRA 
- Migration Readiness Assessment
- AWS CAF(클라우드 채택 프레임워크) 관점에 대한 일련의 질문으로 구성 됨 
- 이러한 질문에는 점수가 매겨지며, 대규모 마이그레이션 및 디지털 전환에 대한 조직의 준비 상태를 평가하는 데 도움이 됨.
- 고객사가 잘 해줘야 마이그레이션의 속도 효율성이 높아짐

# 성공적인 migration modernization
## Simple three-step approach
```
경험
  |                        migrate & modernize
  |
  |            mobilize
  |   assess           
  ---------------------------------------------- 시간
```
- assess : 비즈니스 및 IT 변화의 필요성 및 정당성 확보
- mobilize : 경험 기반의 마이그레이션 실행 준비
- migrate & modernize : 대규모 마이그레이션 진행 및 modernization

### Assess
- rapid discovery, TCO report, **교육!**, Migration reediness Assessment
- 꽤 긴 시간 소요
- Assess 단계가 부족하면 프로젝트 비용초과, 인적자원 부족 등
#### Migration reediness Assessment
- 대규모 클라우드 마이그레이션 준비 상태 및 변화 준비 상태 측정
- 워크샵 방식으로 진행
- 주요 결과물
  - migration 적합도 평가 결과(이해도 , 활용도 등)
  - 예상되는 문제점 
  - 총점 과제도출

#### TCO 분석
- Discovery Tool 사용을 통한 좀 더 정확한 마이그레이션 대상 선정
- TCO분석을 통한 비용절감 방안 검토
- 내부 경영진 보고를 위한 클라우드 마이그레이션 상세 비용 분석 지원 가능

### Mobilize
- 경험을 기반으로 **실행계획**을 수립. 얼만큼 AWS의 서비스로 초석을 다지는지
- 중요!
  - 1) **7 R 패턴**을 어떻게 가져갈지
  - 2) define team model and agile work streams - 클라우드 마이그레이션 진행 협의체 필요
 
# AWS All in migration 소개
- 모더나제이션에서는 무조건 애자일 방법으로!
- mobilize 단계를 매우 큰 비중으로 가져감
