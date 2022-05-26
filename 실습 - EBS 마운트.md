# EBS 생성 및 마운트
> ### \[INDEX]
> 1. EBS 볼륨 생성
> 2. 인스턴스에서 마운트

## EBS 볼륨 생성
- 연결할 인스턴스가 존재하는 가용영역에 생성
  
  ![image](https://user-images.githubusercontent.com/79209568/170394135-826a8958-2de7-4f18-ab34-edf7109eb238.png)
  
  - **IOPS** : 볼륨이 지원할 수 있는 요청된 초당 I/O 작업 수. 프로비저닝 된 IOPS SSD(io1, io2) 및 범용 SSD(gp2, gp3)볼륨의 경우에만 적용 됨
  - **처리량** : 볼륨이 지원할 수 있는 처리량 성능. 처리량에 최적화 된 HDD(st1), 콜드 HDD(sc1), 범융 SSD(gp3)볼륨의 경우에만 적용 됨
  - **암호화** : AWS KMS 키를 사용하여 EBS 볼륨을 암호화

- 방금 생성한 볼륨을 EC2 서버에 연결  
  
  ![image](https://user-images.githubusercontent.com/79209568/170394759-c0076968-e485-4ffd-8cb0-6d0d85a53727.png)
  
  ![image](https://user-images.githubusercontent.com/79209568/170394857-0d3bb73e-8326-4631-b166-d270d9bfd1ee.png)
  - **디바이스 이름** : 기본 값은 권장 디바이스 이름. 루트 볼륨의 경우 /dev/sda1, 데이터 볼륨의 경우 /dev/sd\[f-p]

## 인스턴스에서 마운트
### 볼륨 확인
