# EBS 생성 및 마운트
> ### \[INDEX]
> 1. [EBS 볼륨 생성](#EBS-볼륨-생성)
> 2. [인스턴스에서 마운트](#인스턴스에서-마운트)
>   - [볼륨 확인](#볼륨 확인)
>   - [파일시스템 포맷](#파일시스템-포맷)
>   - [Mount](#Mount)
>   - [Auto Mount](#Auto-Mount)

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
- 연결 확인
  ```
  lsblk
  ```
  ![image](https://user-images.githubusercontent.com/79209568/170395281-ed84dca4-7996-44c7-88c7-36378d256a9d.png)
- 파일시스템 확인 : file -s 장치명
  - **data**라고 나오면 **파일시스템이 존재하지 않는 것**
  ```
  sudo file -s /dev/nvme1n1
  ```
  ![image](https://user-images.githubusercontent.com/79209568/170395562-e45af767-cf0e-4098-a4d8-560d928dbc69.png)

### 파일시스템 포맷
> #### 파일 시스템 ( File System ) : 파일이나 자료를 쉽게 발견 및 접근할 수 있도록 보관 / 조직하는 체제
- 파일시스템을 생성
  ```
  sudo mkfs -t ext4 /dev/nvme1n1
  ```
  ![image](https://user-images.githubusercontent.com/79209568/170398249-3bd38dff-49b5-4613-bd25-ddac6743839e.png)

  > #### 파일시스템 종류
  > - ext : 초기 리눅스에서 사용하였던 종류, 현재는 사용하지 않음
  > - ext2 : 현재도 사용하며, 긴 파일시스템 이름을 지원하는 것이 특징
  > - ext3 : 저널링 파일시스템, ext2보다 파일시스템의 복수/보안 기능이 크게 향상
  > - ext4 : 16TB까지만 지원하던 ext3과는 달리 더 큰 용량을 지원하며, 삭제된 파일 복구,   파일 시스템 속도가 훨씬 빨라진 파일시스템
  > - swap : swap공간으로 사용되는 파일 시스템
  > - xfs : 64bit 고성능 저널링 파일 시스템
  > - iso9660 : DVD/CD-ROM을 위한 표준 파일 시스템으로 읽기만 가능
  > - nfs : 원격 서버에서 파일 시스템 마운트 할 때 사용하는 시스템 (Network File System)

- 생성한 파일시스템 확인
  ```
  sudo file -s /dev/nvme1n1
  ```
  ![image](https://user-images.githubusercontent.com/79209568/170398372-8f68446d-673f-4b93-b4eb-667abbdd08b1.png)

### Mount
> #### 파일 시스템까지 포맷한 볼륨을 사용하기 위해 마운트 해준다.
- 마운트 포인트 디렉토리 생성
  ```
  sudo mkdir /nvme1n1
  ```
- 마운트
  ```
  sudo mount /dev/nvme1n1 /nvme1n1
  ```
- 파일시스템 테스트  
  ![image](https://user-images.githubusercontent.com/79209568/170399186-5d799daa-f65f-4dfe-b0a1-015a7f585bdc.png)  
  ![image](https://user-images.githubusercontent.com/79209568/170399279-e36a77f9-9d73-42e0-ad5c-b7f8fb27f655.png)
- 파일시스템 사용량 확인
  ```
  df -h
  ```
  ![image](https://user-images.githubusercontent.com/79209568/170399545-f9207b8a-06dc-4d48-b48d-1e2c01f390bb.png)

### Auto Mount
> - 시스템 재부팅 시에도 자동으로 해당 볼륨을 마운트 시켜주도록 설정
- fstab 파일을 편집하기 전 백업 해 둠 
  - fstab : file system table. 파일시스템 정보를 저장하고 있으며 마운트 정보를 적는 테이블
  ```
  sudo cp /etc/fstab /etc/fstab.bak
  ```
- `/etc/fstab` 편집
  - 장치명 대신 **UUID를 사용하여 설정**하는 것이 좋다. 장치명의 경우는 상황에 따라 변경될 가능성이 있지만 UUID는 볼륨 파티션에 할당 된 유일한 ID 이기 때문이다.
    > - UUID 확인 : file -s /dev/nvme1n1
  ```
  sudo vi /etc/fstab
  ```
  ![image](https://user-images.githubusercontent.com/79209568/170401644-80cf1678-bdaa-4dd9-82d1-978e86a9ce0b.png)
- 마운트 설정 확인 : 명령어 입력 시 아무 오류가 나지 않는다면 성공
  ```
  sudo mount -a
  ```
- 재부팅 후 확인  
  ```
  df -h
  ```
  ![image](https://user-images.githubusercontent.com/79209568/170401987-e646ef55-54ae-44a1-9aed-a9905f43845b.png)
