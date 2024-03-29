# EC2 부하테스트
> #### \<INDEX>
> 1. [EC2 생성]()
> 2. [ALB 생성](#ALB-생성)
> 3. [EC2 서버에 Apache 설치](#EC2-서버에-Apache-설치)
> 4. [ALB 모니터링 확인](#ALB-모니터링-확인)

![image](https://user-images.githubusercontent.com/79209568/168754961-c12cf825-1efc-47d6-ba76-2c4097196b25.png)

![image](https://user-images.githubusercontent.com/79209568/168755027-e2de6fcf-2251-43da-acd4-db080fc70fe5.png)


## EC2 생성

## ALB 생성
- EC2 \> 로드 밸런서 \> 로드 밸런서 생성  
  
  ![image](https://user-images.githubusercontent.com/79209568/168715689-3ca29da6-5d79-49f9-bb12-3da69039774f.png)
- Application Load Balancer 생성  
  
  ![image](https://user-images.githubusercontent.com/79209568/168715856-a8b1737d-dc8a-4a47-aa30-ca025a2d96aa.png)
### 기본구성
![image](https://user-images.githubusercontent.com/79209568/168716049-8bc880b2-2557-439f-a29f-4006dbdf9e2b.png)
### 네트워크 매핑
- `VPC`는 앞서 생성해 놓은 VPC를 선택
- 가용영역 선택 **꼭 2개 이상!**
  - 가용영역 선택은 ALB를 놓는 위치를 지정하는 것
- 인터넷 경계 스키마 로드 밸런서 이므로 **꼭 public 서브넷 선택**  
  
  ![image](https://user-images.githubusercontent.com/79209568/168716115-53128117-9e39-4e2e-a7b8-d7d675be3465.png)
### 보안그룹 선택
- 보안그룹 생성
  - 기본 세부 정보
    ![image](https://user-images.githubusercontent.com/79209568/168768215-743a0658-a940-4104-94aa-175b23ca0067.png)
- 인바운드 규칙
  - ALB는 인터넷에서 모두 받아와야하므로 0.0.0.0/0으로 열어놓는다  
    
    ![image](https://user-images.githubusercontent.com/79209568/168768964-ce598b8c-79ff-4f9a-af0a-8e1896f88b19.png)


> #### EC2 인스턴스의 보안그룹!!
> - 인바인드 규칙의 소스를 아이피가 아닌 ALB의 보안 그룹으로 설정해야 IGW -\> ALB -\> EC2 웹서버의 경로로 들어간다.
>   
>   ![image](https://user-images.githubusercontent.com/79209568/168770397-067e7fe1-1de7-4aa1-ae5e-a312211d3dfa.png)



### 리스너 및 라우팅
- 대상 그룹 생성 클릭  
  
  ![image](https://user-images.githubusercontent.com/79209568/168716837-a1f94f44-4c03-4183-a502-72aef244e4be.png)
- 대상 유형 : 인스턴스
- 나머지 세부 정보 설정  
  
  ![image](https://user-images.githubusercontent.com/79209568/168717096-fbe96101-2865-4eb6-be7b-2b0499e54670.png)

- 상태 검사  
  
  ![image](https://user-images.githubusercontent.com/79209568/168717143-1ec0293a-7297-41e3-b65e-e041a75f6648.png)
  > ##### 변경 함!
  > - 싱테 검사 경로 `/health/healthcheck.html`로 변경 후,  
  >   ![image](https://user-images.githubusercontent.com/79209568/168734995-accd6c99-d294-44ec-92b6-5dce045faa2f.png)
  > - 아파치 루트 위치로 이동 : `cd /var/www/html/`
  > - health 디렉터리 생성 : `sudo mkdir health`
  > - healthcheck.html 파일 생성 : `sudo vi healthcheck.html`
  > ##### [헬스체크확인](#헬스-체크-확인)

- 대상 등록  
  
  ![image](https://user-images.githubusercontent.com/79209568/168717329-d2672ea1-849a-44e6-9b6c-a6c6dee2b9f6.png)
- 다시 로드 밸런서 생성 페이지로 돌아와서 새로고침 후 방금 생성한 **대상 그룹 선택**  
  
  ![image](https://user-images.githubusercontent.com/79209568/168717510-33bc81d4-295f-40db-954d-7df13b4efcf7.png)

### 로드 밸런서 생성 완료
- `프로비저닝 중` -\> `활성`  
  
  ![image](https://user-images.githubusercontent.com/79209568/168717937-39772f3e-de19-4e4f-98fb-e962a957f6eb.png)


## EC2 서버에 Apache 설치
- SSH 서버로 AWS EC2에 접속(mobaXterm 사용)  
  
  ![image](https://user-images.githubusercontent.com/79209568/168714275-f6538268-8389-4296-b6cb-e4d0182fa51d.png)
- ~~모든 소프트웨어 패키지를 최신 상태로 업데이트~~ - **절대 하지말기**
  ```
  sudo yum update -y
  ```
- Apache 설치
  ```
  sudo yum install httpd -y
  ```
- Apache 시작
  ```
  sudo systemctl start httpd
  ```
- enable 설정 : Apache 웹 서버가 시스템이 부팅될 때마다 시작되도록 한다.
  ```
  sudo systemctl enable httpd
  ```
- 보안 규칙 추가 : http(80포트) 연결을 허용하는 인바인드 보안 규칙을 추가
  - 인스턴스의 보안 그룹 클릭  
    
    ![image](https://user-images.githubusercontent.com/79209568/168714785-62137b9f-67ce-42fc-b716-975213e47ce5.png)
  - 인바인트 규칙 편집 \> 규칙 추가  
    
    - 인바인드 규칙의 소스를 아이피가 아닌 ALB의 보안 그룹으로 설정해야 IGW -\> ALB -\> EC2 웹서버의 경로로 들어간다.    
      ![image](https://user-images.githubusercontent.com/79209568/168770397-067e7fe1-1de7-4aa1-ae5e-a312211d3dfa.png)



## 확인
### 아파치 접속
- 로드밸런서의 DNS 이름으로 접속하면 테스트 페이지가 연결됨  
  ![image](https://user-images.githubusercontent.com/79209568/168771515-09530740-6993-459e-aced-45c50e3794f4.png)
- 접속 확인  
  ![image](https://user-images.githubusercontent.com/79209568/168771738-9e05189f-5fef-4653-a33b-6c08989bbef1.png)
### 헬스 체크 확인
- 페이지 체크  
  ![image](https://user-images.githubusercontent.com/79209568/168771964-503c27e7-60cd-44f3-b779-e3e4a708651d.png)

- 타겟그룹 상태 확인  
  ![image](https://user-images.githubusercontent.com/79209568/168735332-df7f5564-7394-41b7-a6ad-6915d2b67e96.png)

<hr> 

↓ 아래는 뻘짓. 왜냐면 ALB를 통하지 않고 ec2로 바로 접속한 상태를 모니터링 했다^^

## ALB 모니터링 확인
- 대답 응답 시간 : 0.514 ms (11시 10분)
  
  ![image](https://user-images.githubusercontent.com/79209568/168718651-df6779e0-b683-4ca4-9ca6-3ede4ce8a42c.png)

- 요청 : 1개(11시 10분)  
  
  ![image](https://user-images.githubusercontent.com/79209568/168718498-0e6e8032-9f98-407c-911b-3dbde06c9797.png)
- HTTP 4XXs : 1개(11시 10분)  
  
  ![image](https://user-images.githubusercontent.com/79209568/168718783-3e7ee02d-e0ea-417a-8f5e-38a944226db4.png)
- ELB 4XXs : 1개 (11시 2분)  
  
  ![image](https://user-images.githubusercontent.com/79209568/168719036-463282ef-1efc-4f8d-944e-4c251ef77591.png)
- 활성 연결 개수
  - 1개 (11시 2분)  
  - 2개 (11시 10분)  
    ![image](https://user-images.githubusercontent.com/79209568/168719239-702995f4-bcbd-43b3-af19-9156113a744f.png)
    ![image](https://user-images.githubusercontent.com/79209568/168719264-4d86ca11-751d-417e-828f-efa668eca67c.png)
- 새 연결 개수
  - 2개 (11시 2분)  
  - 2개 (11시 10분)  
    
    ![image](https://user-images.githubusercontent.com/79209568/168719398-729151af-f1b0-4a06-b3f0-7d41534af7c8.png)

    ![image](https://user-images.githubusercontent.com/79209568/168719418-8c47abe3-9108-4546-9ba3-2eaf7f99e4c0.png)

- 처리된 바이트
  - 588 bytes (11시 2분)  
  - 4226 bytes (11시 10분)  
    ![image](https://user-images.githubusercontent.com/79209568/168719470-bb754dbf-1260-436d-a651-b123ea51c39e.png)

    ![image](https://user-images.githubusercontent.com/79209568/168719486-92392efe-1171-4f76-9b3d-289e900c7e18.png)

- 사용된 Load Balancer 용량 단위
  - 0.00002222222222222222 개 (11시 2분)
  - 0.00002222222222222222 개 (11시 10분)  
    
    ![image](https://user-images.githubusercontent.com/79209568/168719536-9c3ff130-4c54-4cca-927f-8933c92b2054.png)

### 정리
|-|11시 2분|11시 10분|
|--|--|--|
|대답 응답 시간(밀리세컨)|-|0.514ms|
|요청(개수)|-|1개|
|HTTP 4XXs(개수)|-|1개|
|ELB 4XXs(개수)|1개|-|
|활성 연결 개수(개수)|1개|2개|
|새 연결 개수(개수)|2개|2개|
|처리된 바이트(바이트)|588 bytes|4226 bytes|
|사용된 Load Balancer 용량 단위(개수)|0.00002222222222222222 개|0.00002222222222222222 개|
