# EC2 부하테스트
> #### \<INDEX>
> 1. [EC2 생성]()
> 2. [EC2 서버에 Apache 설치](#EC2-서버에-Apache-설치)
> 3. [ALB 생성]()

## EC2 생성

## EC2 서버에 Apache 설치
- SSH 서버로 AWS EC2에 접속(mobaXterm 사용)  
  ![image](https://user-images.githubusercontent.com/79209568/168714275-f6538268-8389-4296-b6cb-e4d0182fa51d.png)
- 모든 소프트웨어 패키지를 최신 상태로 업데이트 (처음 한 번)
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
    ![image](https://user-images.githubusercontent.com/79209568/168714918-3318a9f5-bc51-431f-8b85-3b407e8d2d5e.png)
- Apache 접속
  - EC2 IP로 접속하면 테스트 페이지가 연결됨 (`http://54.64.211.25/`)
    ![image](https://user-images.githubusercontent.com/79209568/168715076-29bcd36b-f331-4fdb-b182-60dc150cc6c6.png)

 


