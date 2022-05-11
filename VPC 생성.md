# VPC 설정
- IPv4 CIDR 블록
![image](https://user-images.githubusercontent.com/79209568/167778493-300e390d-b9d0-46a0-adba-7fa0281d8c96.png)
- 사설 IP 대역  
  ![image](https://user-images.githubusercontent.com/79209568/167784448-1da950d1-4efe-4e4b-b2c8-beb672549cab.png)
  - 10 은 CIDR 16, 172 는 CIDR 24 많이 사용
  
- 테넌시
  - EC2 인스턴스가 물리적 하드웨어에 분산되는 방식을 정의
  - 기본 : 여러 AWS 계정이 동일한 물리적 하드웨어를 공유
  - 전용 : 인스턴스가 완전히 전용으로 사용할 EC2 인스턴스 용량을 가진 물리적 서버에서 실행 됨
