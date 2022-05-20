# Cloud Economics
## The Big Five
- Rightsizing : Choose the optimal sized instance
- Scheduling : Turn them off if you dont use them
- Pricing : on demand is easy but not the best
- Storage : leverage different storage classes
- Monitoring : set goals and take actions

### Rigtsizing
M4.4xlarge (0.984) M4.xxlarge (0.246) 비용이 1/4로 줄어듦
#### Rigatsizing, 어떻게 검사?
1. 인스턴스 실행
2. 매트릭 확인
3. 인스턴스 사이즈 재조정
4. 재확인
5. 비용절감
#### 콘솔로도 확인
- Comput Optimzier : CPU가 오버 스펙인지 확인

### Scheduling
- 주말, 업무 시간에 끔
### Pricing
- 온디맨드 : 약정 없이 사용한 EC2인스턴스에 대한 비용만 지불
  - 트래픽 예측 불가, 비즈니스 환경 자주 변경
- RI/SP : 1년/3년 약정으로 온디맨드 대비 최대 75퍼 절약
  - 일정한 워크로드 및 항시 켜두어야 하는 워크로드 
  - 특정 패밀리 사용 조건이 있어서 조건에 맞는 타입으로 사야 예약 인스턴스 적용이 됨
  - 24시간 돌아가야 하는 것에 RI가 좋다. 껐다 켰다 하는 워크로드에는 별로
  - EC2, RDS, RedShift, ELB, ElasticSearch
  - **Instance Size Flexi bility** : m3.large 인스턴스 RI를 구매하면 m3.medium 인스턴스에도 적용. 혹은 m3.medium 두개 구매하면 m3.large가능
    - RI는 크기가 같거나 더 작은 패밀리의 모든 인스턴스에 적용 됨
      `m3.large 인스턴스에 대한 RI를 구매하는 경우 RI는 m3.medium 인스턴스에도 적용`
    - 여러 개의 작은 RI를 결합하여 더 큰 인스턴스에 적용 됨
      `두 m3.medium 인스턴스에 대해 두 개의 RI를 구입하는 경우 이러한 RI는 m3.large 인스턴스에도 적용`
    - billing 될 때 알아서 처리 됨
- 스팟 인스턴스 : 예비 컴퓨팅 용량을 통해 온디맨드 대비 최대 90퍼 절약
  - 시간 제한없는 배치성 워크로드
  - 쓰다가 꺼질 수 있지만 2분 전에 알람을 울림
  - 하지만 차단되는 중단 빈도는 적다. (중단빈도는 AWS Website에서 확인 가능)

#### RI, SP(Savings Plan) 비교
![image](https://user-images.githubusercontent.com/79209568/169453757-ebe386b7-b8d1-40b2-a778-9c0635e137b8.png)

### Storage
![image](https://user-images.githubusercontent.com/79209568/169450606-7b4215cd-7a84-4ee0-9480-ed3a2affea85.png)

#### 블록 스토리지 클래스 비교
- IOPS : 빈번한 접속
  - gp2, gp3, io1, io2
- Throughput : 빈번하지 않은 접속
  - st1, sc1

### Monitoring
- Trusted Advisor : 리스트를 뽑아줌
  - Cost Optimizing : 놀고 있는 인스턴스를 찾아줌
  - Performance
  - Security
  - Fault Tolerance

- AWS Budget : 어느 이상 요금, 몇 개 이상 인스턴스를 쓰게 되면 알림을 줌

<hr>


- applied hourly
  - m5.xlarge RI로 10개를 샀는데 온디맨드로 5개는 씀
- AWS organization : 루트 계정이 있고 linked account들이 쓰는 돈을 농심 계정이 냄. 이 조직도가 organization
  - 루트 계정이 RI/SP를 못쓰면 linked accouunt들이 씀
  - RI/SP는 oraganization 단위로 묶임

- RI 구입 시 넣는 조건
  - 패밀리, 리전 단위(리전 지정됨)

- Standard
  - most significant discount 
  - best suited for steady usage
  - limited flexiblility

- Convertible
  - instance family
  - instance size 
  - operating systems 
  - tenancy 
  - go for higer upfront payment option (down X)

- RI purchase recomman - Cost Explorer

### Savings plan
- 시간 당 요금 기준으로 구매 (인스턴스 요금 기준 말고)
- 이점
  - 사용이 쉽다 (t2를 구매해야하는데 t3로 잘못 구매. 이런 것을 신경 쓸 필요없음)
  - 할인율이 높다 (ec2는 72%, fargate 66%, lambda 17%, sagemaker 64%), 가장 할인율이 높은 서비스부터 할인 됨
 - 주로 컴퓨팅 쪽
 - **옵션**
   - compute saving plans : 람다, ec2 상관 X 다 적용
   - EC2 Instance Savings Plan : size, os, tenancy (RI스러운 옵션)
   - Sagemaker Savings Plan

- Organizations (Payer, Linked 1, Linked 2, Linked 3인 상황)
  - Account1 에서 쓰고 남은 걸 Payer 주고 또 남은 걸 다른 linked 나눠줌
    - Linked 1에서 Savings plan을 산 경우, 
  - payer 계정에서 구매하는것이 제일 이익

- Capacity Reservatoins, RI, SP
  - Capacity Reservatoins : 대규모 배포를 위해 미리 잡아놓기 위하는 용도로 사용
  - term : No commitment reauired, can be created and canceled as needed. / Requieres a fixed one year or tree year commitment
  - capacity benefit??

- 할인율 적용 순서
  - Zonal RI, Regional non size flexible RI ... -> SP




